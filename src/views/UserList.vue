<template>
    <base-spinner v-if="!finishedLoading"></base-spinner>
    <div v-else class="row">
        <UserProfile
            class="col-4"
            :username="$store.state.userListName"
            :userImgUrl="
                $store.state.userListAvatar ||
                    'https://placewaifu.com/image/200'
            "
        />
        <section class="lists col-8">
            <h2
                class="lists__empty"
                v-if="
                    watchingList.length == 0 &&
                        completedList.length == 0 &&
                        planToWatchList.length == 0
                "
            >
                List is empty
            </h2>
            <AnimeList
                v-if="watchingList.length != 0"
                :type="'Watching'"
                :animeList="watchingList"
            />
            <AnimeList
                v-if="completedList.length != 0"
                :type="'Completed'"
                :animeList="completedList"
            />
            <AnimeList
                v-if="planToWatchList.length != 0"
                :type="'Planning'"
                :animeList="planToWatchList"
            />
        </section>
    </div>
</template>

<script>
import AnimeList from '@/components/UserList/AnimeList'
import UserProfile from '@/components/UserList/UserProfile'

export default {
    props: {
        username: String,
    },
    components: {
        AnimeList,
        UserProfile,
    },
    data() {
        return {
            finishedLoading: false,
        }
    },
    async mounted() {
        await this.$store.dispatch('getUserInfo', this.username)
        await this.$store.dispatch('getUserList', this.username)
        this.finishedLoading = true
    },
    updated() {},
    computed: {
        watchingList() {
            return this.$store.getters.watchingList
        },
        completedList() {
            return this.$store.getters.completedList
        },
        planToWatchList() {
            return this.$store.getters.planToWatchList
        },
    },
}
</script>

<style lang="scss" scoped>
h2 {
    margin-top: 5rem;
    font-size: 2rem;
    color: var(--color-primary);
}
.row {
    display: flex;
    flex-wrap: wrap;
    $columns: 12;
    [class^='col-'] {
        flex-basis: 100%;
    }

    @media (min-width: 1050px) {
        @for $i from 1 through $columns {
            .col-#{$i} {
                flex: 0 0 (100% / ($columns / $i));
            }
            .col-offset-#{$i} {
                margin-left: 100% / (($columns / $i));
            }
        }
    }
}

.lists {
    user-select: none;
    width: 100%;
    align-items: center;
    padding-bottom: 10rem;
    @media (max-width: 1050px) {
        display: flex;
        flex-direction: column;
        justify-content: center;
    }
}

.background {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    z-index: -1;
    background-image: url('https://i.redd.it/mtti5nldvlr51.png');
    background-size: cover;
    background-position: center;
    background-repeat: none;
    filter: blur(8px);
    &::after {
        content: '';
        position: fixed;
        top: -20px;
        left: -20px;
        right: -20px;
        bottom: -20px;
        background: rgba(255, 255, 255, 0.5);
    }
}
</style>
