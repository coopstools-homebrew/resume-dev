<template>
    <SectionTemplate :section-data="props.sectionData">
        <h1>GitHub</h1>
        <button class="btn btn-primary btn-lg" @click="redirectToGitHub">
            <i class="fa-brands fa-github me-2"></i>Login with GitHub
        </button>
    </SectionTemplate>
</template>

<script setup>
import SectionTemplate from "../_templates/SectionTemplate.vue"
import {computed} from "vue"
import {useData} from "../../../composables/data.js"
import {useNavigation} from "../../../composables/navigation.js"

const data = useData()
const navigation = useNavigation()

/**
 * @property {Object} sectionData
 */
const props = defineProps({
    sectionData: Object
})

/**
 * @type {ComputedRef<String>}
 */
const loginTitle = computed(() => {
    return props.sectionData.content['locales']['title']
})

const redirectToGitHub = () => {
    const githubConfig = props.sectionData.content.items.github
    const params = new URLSearchParams({
        client_id: githubConfig.clientId,
        redirect_uri: githubConfig.redirectUri,
        scope: githubConfig.scopes.join(' ')
    })
    window.location.href = `${githubConfig.url}?${params.toString()}`
}
</script>

<style lang="scss" scoped>
@import "/src/scss/_theming.scss";

.login-title {
    margin-bottom: 1rem;
    text-transform: uppercase;
    font-weight: bold;
}

.solid-divider {
    @include media-breakpoint-up($navigation-sidebar-breakpoint) {
        display: none;
    }
}

.info-list {
    @include generate-dynamic-styles-with-hash((
        xxxl: (margin-bottom: 2.5rem),
        lg: (margin-bottom: 2rem),
        md: (margin-bottom: 1.2rem)
    ))
}
</style>
