<template>
  <div class="app-layout">
    <div>
      <div class="container-xxl position-relative p-0">
        <nav class="navbar navbar-expand-lg navbar-light px-4 px-lg-5 py-3 py-lg-0">
          <a href="" class="navbar-brand p-0">
            <h1 class="m-0">
              <span class="fs-5"><router-link to="dashboard">Funds Malaysia</router-link></span>
            </h1>
            <!-- <img src="img/logo.png" alt="Logo"> -->
          </a>
          <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarCollapse">
            <span class="fa fa-bars"></span>
          </button>
          <div id="navbarCollapse" class="collapse navbar-collapse">
            <div class="navbar-nav ms-auto py-0">
              <a href="" class="nav-item nav-link active"><router-link to="dashboard">Home</router-link></a>
              <a href="" class="nav-item nav-link"><router-link to="fundStatusCheck">Fund Status Check</router-link></a>
              <a href="" class="nav-item nav-link"><router-link to="newFundLaunch">New Fund Launch</router-link></a>
              <a href="" class="nav-item nav-link"><router-link to="aboutUs">About</router-link></a>
            </div>
            <a
              href="https://htmlcodex.com/startup-company-website-template"
              class="btn btn-secondary text-light rounded-pill py-2 px-4 ms-3"
              >Sign-In or Sign-Up</a
            >
          </div>
        </nav>
      </div>
    </div>
    <div class="app-layout__content">
      <div class="app-layout__page">
        <div class="layout fluid gutter--xl">
          <router-view />
        </div>
      </div>
    </div>
    <div class="container-fluid bg-primary text-light footer mt-5 pt-5 wow fadeIn" data-wow-delay="0.1s">
      <AppFooter></AppFooter>
    </div>
  </div>
</template>

<script setup>
  import { computed, onBeforeUnmount, onMounted, ref } from 'vue'
  import { storeToRefs } from 'pinia'
  import { onBeforeRouteUpdate } from 'vue-router'

  import { useGlobalStore } from '../stores/global-store'

  import AppFooter from '../components/app-footer/AppFooter.vue'

  const GlobalStore = useGlobalStore()

  const mobileBreakPointPX = 640
  const tabletBreakPointPX = 768

  const sidebarWidth = ref('16rem')
  const sidebarMinimizedWidth = ref(undefined)

  const isMobile = ref(false)
  const isTablet = ref(false)
  const { isSidebarMinimized } = storeToRefs(GlobalStore)
  const checkIsTablet = () => window.innerWidth <= tabletBreakPointPX
  const checkIsMobile = () => window.innerWidth <= mobileBreakPointPX

  const onResize = () => {
    isSidebarMinimized.value = checkIsTablet()

    isMobile.value = checkIsMobile()
    isTablet.value = checkIsTablet()
    sidebarMinimizedWidth.value = isMobile.value ? '0' : '4.5rem'
    sidebarWidth.value = isTablet.value ? '100%' : '16rem'
  }

  onMounted(() => {
    window.addEventListener('resize', onResize)
  })

  onBeforeUnmount(() => {
    window.removeEventListener('resize', onResize)
  })

  onBeforeRouteUpdate(() => {
    if (checkIsTablet()) {
      // Collapse sidebar after route change for Mobile
      isSidebarMinimized.value = true
    }
  })

  onResize()

  // eslint-disable-next-line @typescript-eslint/no-unused-vars
  const isFullScreenSidebar = computed(() => isTablet.value && !isSidebarMinimized.value)

  // eslint-disable-next-line @typescript-eslint/no-unused-vars
  const onCloseSidebarButtonClick = () => {
    isSidebarMinimized.value = true
  }
</script>

<style lang="scss">
  $mobileBreakPointPX: 640px;
  $tabletBreakPointPX: 768px;

  .app-layout {
    height: 100vh;
    display: flex;
    flex-direction: column;
    &__navbar {
      min-height: 4rem;
    }

    &__content {
      display: flex;
      height: calc(100vh - 4rem);
      flex: 1;

      @media screen and (max-width: $tabletBreakPointPX) {
        height: calc(100vh - 6.5rem);
      }

      .app-layout__sidebar-wrapper {
        position: relative;
        height: 100%;
        background: var(--va-white);

        @media screen and (max-width: $tabletBreakPointPX) {
          &:not(.minimized) {
            width: 100%;
            height: 100%;
            position: fixed;
            top: 0;
            z-index: 999;
          }

          .va-sidebar:not(.va-sidebar--minimized) {
            // Z-index fix for preventing overflow for close button
            z-index: -1;
            .va-sidebar__menu {
              padding: 0;
            }
          }
        }
      }
    }
    &__page {
      flex-grow: 2;
      overflow-y: scroll;
    }
  }
</style>
