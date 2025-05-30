<script setup>
  import { ref, computed, onBeforeMount} from 'vue';
  import MglMap from './components/MglMap.vue';
  import FABMain from './components/FABMain.vue';
  import FABButton from './components/FABButton.vue';
  import ResultsPane from './components/ResultsPane.vue';
  import YearSearchBar from './components/YearSearchBar.vue';
  import DynamicGeoJsonLayer from './components/DynamicGeoJsonLayer.vue';
  import VideoModal from './components/VideoModal.vue';

  const emptyGeoJson = {type:'geojson',data:{id: 'search-source', type: 'FeatureCollection', features: []}};

  // State
  const appYear = ref('');
  const geoJson = ref(emptyGeoJson);

  const contrastMode = ref(false);

  // define available years
  const years = [
    { year: '1910', date: '1910-04-15' },
    { year: '1920', date: '1920-01-02' }
  ];

  const fabButtonCustomProps = [
    {
      title: 'Home',
      icon: ['fas', 'house'],
      ariaLabel: 'reset',
      ariaDescription: 'reset map and clear search results',
      clickHandler: () => resetApp(),
    },
    {
      title: 'Help',
      icon: ['fas', 'question'],
      ariaLabel: 'help',
      ariaDescription: 'show help video',
      clickHandler: () => showHelpVideo(),
    },
    {
      title:'Contrast',
      icon:'',
      ariaLabel: 'contrast mode',
      state: contrastMode.value,
      ariaDescription: 'enable high contrast mode',
      innerText: `Contrast ${contrastMode.value ? 'ON' : 'OFF'}`,
      clickHandler: () => toggleContrastMode(),
    }
  ];

  const geoJsonFeaturePaint = {
    'circle-radius': 8,
    'circle-color': '#f37021',     // orange inner circle
    'circle-stroke-color': '#ffffff', // white border
    'circle-stroke-width': 3
  }

  // Create an array of FAB button properties
  //  based on the custom properties defined above
  // This is a computed property that will be reactive to changes in the custom properties
  //  and will return an array of objects with the properties needed for each FAB button
  // The properties include title, icon, delay, ariaLabel, ariaDescription,
  //  shadowX, shadowY, shadowBlurTop, shadowBlurBottom, shadowWidth, shadowColor
  // The delay is calculated based on the index of the button in the array
  // The shadow properties are set to default values
  // The icon is set to the value of the icon property in the custom properties
  // The ariaLabel and ariaDescription are set to the values of the ariaLabel
  //  and ariaDescription properties in the custom properties
  // The state is set to the value of the state property in the custom properties
  const createFabButtonProps = computed(() => {
    return fabButtonCustomProps.map((button, index) => ({
      ...button,
      delay: 0.3 - (index * 0.05),
      shadowX: 3,
      shadowY: 3,
      shadowBlurTop: 0,
      shadowBlurBottom: 1,
      shadowWidth: 0.5,
      shadowColor: '#eee',
      innerText: button.title === 'Contrast' ? `Contrast ${contrastMode.value ? 'ON' : 'OFF'}` : button.innerText, // Dynamically update innerText
    }));
  });

  // Map and ResultsPane ref
  const mglMapRef = ref(null);
  const resultsPaneRef = ref(null);
  const map = ref(null);
  const videoModalRef = ref(null);
  const helpVideoUrl = `${import.meta.env.BASE_URL}GCC-Kiosk-April-2025.mp4`;

  // Reset Functions

  // Reset handler for app state and map
  function resetApp() {
    // Reset app year to default
    updateYear('');

    // Reset search results
    clearResults();

    // Reset map zoom and center to default values
    resetMap();
  }

  function clearResults() {
    if (resultsPaneRef && resultsPaneRef.value) {
      resultsPaneRef.value.resetState();
    }
  }

  function resetMap() {
    if (mglMapRef && mglMapRef.value) {
      mglMapRef.value.resetMap();
      geoJson.value = emptyGeoJson; // Reset geoJson to empty
    }
  }

  // FAB Button Handlers
  // Functions to handle FAB button actions

  function showHelpVideo() {
    // Logic to show help video
    if (videoModalRef.value) {
      videoModalRef.value.openDialog();
    }
  }

  function toggleContrastMode() {
    contrastMode.value = !contrastMode.value;
    // Logic to toggle high contrast mode
    console.log('Toggle high contrast mode');
  }

  // Event Handlers
  // Functions to handle events from child components

  function handleGeojson(newGeojson) {
    if (mglMapRef.value) {
      geoJson.value = newGeojson;
      //mglMapRef.value.loadDynamicLayer(newGeojson);
    }
  }

  function handleSearch(searchValue) {
    clearResults();
    resetMap();
    if (resultsPaneRef && resultsPaneRef.value) {
      resultsPaneRef.value.search(searchValue);
    }
  }

  function handleFeatureClick(feature) {
    console.log('Feature clicked:', feature);
  }

  function updateYear(newYear) {
    appYear.value = newYear;
    console.log('Year updated:', newYear);
    if (resultsPaneRef && resultsPaneRef.value) {
      resultsPaneRef.value.yearChanged(newYear);
    }
  }

  const handleMapCreated = (mbMap) => {
    map.value = mbMap;
  };
</script>

<template>

  <!-- FAB Component to create menu with dynamic buttons-->
  <FABMain>
    <FABButton
      v-for="(item, index) in createFabButtonProps"
      :key="index"
      v-bind="item"
      :state="item.state"
      :innerText="typeof item.innerText === 'function' ? item.innerText() : item.innerText"
      :clickHandler="item.clickHandler"
    ></FABButton>
  </FABMain>

  <!-- Video Modal Component to show help video -->
  <VideoModal ref="videoModalRef" :url="helpVideoUrl" :autoplay="true" class="fab fa-autoprefixer"></VideoModal>

  <!-- YearSelector Component to change year and perform searches -->
  <YearSearchBar :onSearch="handleSearch" :onYearChange="updateYear" :years="years"></YearSearchBar>

  <!-- Map Component with layer containing dynamic GeoJSON search results-->
  <MglMap :year="appYear" ref="mglMapRef" @created="handleMapCreated">
    <DynamicGeoJsonLayer
      :geojson="geoJson"
      :type="'circle'"
      :paint="geoJsonFeaturePaint"
      :layout="{ 'visibility': 'visible' }"
      layerId="search-layer"
      @feature-click="handleFeatureClick"
      :filterYear="appYear"
      :map="map"
    />
  </MglMap>

  <!-- Results Pane Component with containing dynamic search results-->
  <ResultsPane
    ref="resultsPaneRef"
    :years="years"
    :year="appYear"
    @update:geojson="handleGeojson"
    v-model:geojson="geoJson"
  />
</template>

<style>
  .historical-map-container {
    height: 100vh;
    width: 60vw;
  }
  .mgl-map-wrapper {
    height: 97.05vh;
    width: 60vw;
  }
  .dialog {
    border: none;
    border-radius: 8px;
    width: 90vw;
    max-width: 800px;
    padding: 0;
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
    color: #333
  }
  .close-button {
    font-size: 2.5rem;
    background: none;
    border: none;
    cursor: pointer;
    padding: 0;
    color: var(--gcc-lt-green);
  }
  .fabInnerButton[title="Contrast"] {
    background-image: linear-gradient(to right, var(--gcc-dk-green) 50%, var(--gcc-white) 50%);
  }
</style>