<template>
    <div>
        <h1>Страница с постами</h1>
        <!-- <my-input
            v-model="searchQuery"
            placeholder="Поиск..."
            v-focus
        />     -->
        <div class="app__btns">
            <my-button @click="showDialog">
                Создать пост
            </my-button>
            <!-- <my-select
                v-model="selectedSort"
                :options="sortOptionts"
            /> -->
        </div>
        <my-dialog v-model:show="dialogVisible">    
        <post-form
            @create="createPost"
        />
        </my-dialog>
        <post-list
            :posts="sortedAndSearched"
            @remove="removePost"
            v-if="!isPostsLoading"
        />
        <div v-else>Идёт загрузка...</div>
        <div v-intersection="loadMorePosts" class="observer"></div>
        <!-- <div class="page__wrapper">
            <div
                v-for="pageNumber in totalPages"
                 :key="pageNumber"
                 class="page"
                 :class="{
                    'current-page': page === pageNumber
                 }"
                 @click="changePage(pageNumber)"
            >{{ pageNumber }}</div>     
        </div> -->
    </div>
</template>

<script>
import PostForm from "@/components/PostForm.vue";
import PostList from "@/components/PostList.vue";
import MyDialog from "@/components/UI/MyDialog.vue";
import MyButton from "@/components/UI/MyButton.vue";
import MySelect from "@/components/UI/MySelect.vue";
import MyInput from "@/components/UI/MyInput.vue";
import axios from "axios";
import {mapState, mapMutations, mapActions, mapGetters} from 'vuex';
export default {
    components: {
        MyButton,
        MyDialog,
        MySelect,
        MyInput,
        PostList, PostForm
    },
    data() {
        return {
            dialogVisible: false,
        }
    },    
    methods: {
        ...mapMutations ({
            setPage: 'post/setPage'
        }),
        ...mapActions({
            loadMorePosts: 'post/loadMorePosts',
            fetchPosts: 'post/fetchPosts'
        }),
        createPost(post) {
            this.posts.push(post);
            this.dialogVisible = false;
        },
        removePost(post) {
            this.posts = this.posts.filter(p => p.id !== post.id)
        },
        showDialog() {
            this.dialogVisible = true;
        },
        // changePage(pageNumber) {
        //     this.page = pageNumber
        // }, 
        async fetchPosts() {
            try {
                this.isPostsLoading = true;
                const response = await axios.get('https://jsonplaceholder.typicode.com/posts?_limit=10', {
                    params: {
                        _page: this.page,
                        _limit: this.limit
                    }
                });
                this.totalPages = Math.ceil(response.headers['x-total-count'] / this.limit)
                this.posts = response.data;
            }   catch (e) {
                alert('Ошибка')
            } finally {
                this.isPostsLoading = false;
            }
        },
        async loadMorePosts() {
            try {
                this.page += 1;
                const response = await axios.get('https://jsonplaceholder.typicode.com/posts?_limit=10', {
                    params: {
                        _page: this.page,
                        _limit: this.limit
                    }
                });
                this.totalPages = Math.ceil(response.headers['x-total-count'] / this.limit)
                this.posts = [...this.posts, ...response.data];
            }   catch (e) {
                alert('Ошибка')
            }
        }
    },
    mounted() {
        // this.fetchPosts();
        // const options = {
        //     rootMargin: '0px',
        //     threshold: 1.0
        // }
        // const callback = (entries, observer) => {
        //     if(entries[0].isIntersecting && this.page < this.totalPages) {
        //         this.$loadMorePosts()
        //     }    
        // };
        // const observer = new IntersectionObserver(callback, options);
        // observer.observe(this.$refs.observer);
    },
    computed: {
        ...mapState({
            post: state => state.post.posts,
            isPostsLoading: state => state.post.isPostsLoading,
            selectedSort: state => state.post.selectedSort,
            searchQuery: state => state.post.searchQuery,
            page: state => state.post.limit,
            limit: state => state.post.totalPages,
            sortOptionts: state => state.post.sortOptionts
        }),
        ...mapGetters({
            sortedPosts: 'post/sortedPosts',
            sortedAndSearchedPosts: 'post/sortedAndSearchedPosts'
        })
        // sortedPosts() {
        //     return [...this.posts].sort((post1, post2) => {
        //         return post1[this.selectedSort]?.localeCompate(post2[this.selectedSort])
        //     })
        // },
        // sortedAndSearched() {
        //     return this.sortedPosts.filter(post => post.title.toLowerCase().includes(this.searchQuery.toLowerCase()))
        // }
    },
    watch: {
        // page() {
        //     this.fetchPosts()
        // }
    }
}
</script>

<style>

.app__btns {
    margin: 15px 0;
    display: flex;
    justify-content: space-between;
}

.page__wrapper {
    display: flex;
    margin-top: 15px;
}

.page {
    border: 2px solid thistle;
    padding: 10 px;
    margin: 3px;
}

.current-page {
    border: 3px solid blueviolet;
}

.observer {
    height: 30px;
    background: rgb(249, 175, 175);
}
</style>