<template>
    <SectionTemplate :section-data="props.sectionData">
        <h1>GitHub</h1>
        <div v-if="loadingComplete">
            <button v-if="!isAuthenticated" class="btn btn-primary btn-lg" @click="redirectToGitHub">
                <i class="fa-brands fa-github me-2"></i>Login with GitHub
            </button>
            <div v-else class="mt-4 d-flex align-items-center">
                <img :src="avatarUrl" alt="GitHub Avatar" class="rounded-circle" style="width: 24px; height: 24px;" />
                <span class="ms-2">{{ orgName }}</span>
            </div>
        </div>
    </SectionTemplate>
</template>

<script setup>
import SectionTemplate from "../_templates/SectionTemplate.vue"
import {computed, ref, onMounted} from "vue"
import {useData} from "../../../composables/data.js"
import {useNavigation} from "../../../composables/navigation.js"

const data = useData()
const navigation = useNavigation()
const avatarUrl = ref(null)
const loadingComplete = ref(false)
const isAuthenticated = ref(false)
const orgName = ref('')

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

const fetchUserData = async () => {
    try {
        const response = await fetch('https://ce4b-104-129-206-200.ngrok-free.app/data', {
            credentials: 'include'
        })

        if (response.status >= 500) {
            throw new Error(`Server error: ${response.status}`)
        }

        if (response.status === 400 || response.status === 401) {
            isAuthenticated.value = false
            return
        }

        if (response.status < 400) {
            const userData = await response.json()
            if (userData.avatar_url) {
                avatarUrl.value = userData.avatar_url
                orgName.value = userData.org_name || ''
                isAuthenticated.value = true
            }
        }
    } catch (error) {
        console.error('Error fetching user data:', error)
        throw error // Re-throw server errors
    } finally {
        loadingComplete.value = true
    }
}

onMounted(() => {
    fetchUserData()
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
