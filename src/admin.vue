<template>
    <div id="app" :class="backend ? 'backend' : 'frontend'">
        <Navigation :backend="backend"></Navigation>
        <div class="main wrap">
            <div class="main-left">
                <div class="home-feeds cards-wrap">
                    <transition :name="appShell.pageTransitionName" @before-enter="handleBeforeEnter" @after-enter="handleAfterEnter">
                        <keep-alive :include="cacheComponents">
                            <router-view :key="$route.fullPath" class="app-view"></router-view>
                        </keep-alive>
                    </transition>
                </div>
            </div>
            <backend-menu v-if="!isLogin"></backend-menu>
        </div>
    </div>
</template>
<script>
import { mapGetters } from 'vuex'
import NProgress from 'nprogress'
import Navigation from './components/navigation.vue'
import backendMenu from './components/backend-menu.vue'

export default {
    name: 'backend',
    components: {
        backendMenu,
        Navigation
    },
    data() {
        return {
            cacheComponents: 'backend-admin-list, backend-article-comment, backend-article-list, backend-user-list'
        }
    },
    computed: {
        ...mapGetters({
            global: 'global/get',
            appShell: 'appShell/get'
        }),
        key() {
            return this.$route.path.replace(/\//g, '_')
        },
        backend() {
            return this.$route.path.indexOf('backend') >= 0
        },
        isLogin() {
            return ['/backend', '/backend/'].includes(this.$route.path)
        }
    },
    watch: {
        'global.progress'(val) {
            if (val === 0) {
                NProgress.set(0)
                NProgress.start()
            } else if (val === 100) {
                NProgress.done()
            } else {
                NProgress.set(val / 100)
                NProgress.start()
            }
        }
    },
    methods: {
        handleBeforeEnter() {
            this.$store.dispatch('appShell/setPageSwitching', true)
        },
        handleAfterEnter() {
            this.$store.dispatch('appShell/setPageSwitching', false)
        },
        handleClickHeaderBack() {
            this.$router.go(-1)
        }
    }
}
</script>
