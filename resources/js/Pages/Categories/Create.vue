<template>
    <pageTitle title="Create" />
    <breeze-authenticated-layout :isAdmin="isAdmin" :orderCount="orderCount" :doneOrder="doneOrder">
        <div class="container-fluid px-0 bg-gray-700">
            <div class="grid lg:grid-cols-12 w-full">

                <div class="lg:col-start-4 lg:col-end-13 lg:mx-2 text-center mt-4">
                    <Title text="Create" />

                    <form class="mt-5 space-y-3 w-75 mx-auto" @submit.prevent="createCat" enctype="multipart/form-data" method="POST">

                        <label for="cat_name" class="block text-left text-white mr-2 mb-2">Name</label>
                        <div class="mt-1 relative rounded-md shadow-sm">
                            <input id="cat_name" type="text" v-model="categories.cat_name" :class="{ 'is-invalid': errors.cat_name }" class="px-3 bg-gray-600 text-gray-300 focus:ring-indigo-500 focus:border-indigo-500 block w-full pl-7 pr-12 sm:text-sm border-gray-300 rounded-md" />
                        </div>
                        <small class="text-red-300 mr-auto mb-3">{{ errors.cat_name }}</small>

                        <label for="img" class="block text-left text-white mr-2 mb-2">Image</label>
                        <div class="bg-gray-400 py-3 rounded-1 cursor-pointer border-dashed border-2">
                            <div @click="selectImage">
                                <fa icon="images" class="text-4xl" />
                            </div>
                            <input name="img_url" ref="fileInput" type="file" hidden="true" accept="image/*" @change="inputImage">
                            <div v-show="img_preview" class="inline-flex">
                                <span @click="removeImgPreview(img_preview)" class="relative X rounded-pill px-2 mt-1 mb-auto opacity-80 text-white bg-red-700 h-full"><fa icon="times" /></span>
                                <img loading="lazy"  @click="showImage(img_preview)" :src="img_preview" width="100" height ="100" class="img-thumbnail cursor-zoom-in">
                            </div>
                        </div>
                        <small class="text-red-300 mr-auto mb-3">{{ errors.img_url }}</small>

                        <div class="flex justify-around my-4">
                            <button :disabled="categories.processing || !categories.cat_name || !categories.img_url" type="submit" class="btn btn-outline-primary px-5 text-white">Create</button>
                        </div>

                    </form>

                </div>
            </div>
        </div>
    </breeze-authenticated-layout>
</template>

<script>
    import BreezeAuthenticatedLayout from '@/Layouts/Auth/Authenticated'
    import sideBar from "../../Layouts/Template/navbar"
    import { useForm } from '@inertiajs/inertia-vue3'
    import Title from "../../Layouts/Template/titles";
    import pageTitle from "../../Layouts/Template/pageTitle";

    export default {
        components: {
            BreezeAuthenticatedLayout,
            sideBar,
            Title,
            pageTitle,
        },
        data(){
            return{
                success: null,
                imgExt: ['png', 'jpg', 'bmp', 'jpeg', 'gif', 'tiff', 'jfif'],
                img_preview: null
            }
        },
        setup () {
            const categories = useForm({
                cat_name: null,
                img_url: null,
            })

            function createCat() {
                categories.post('/categories')
            }

            return { categories, createCat }
        },
        props: {
            auth: Object,
            errors: Object,
            isAdmin: null,
            orderCount: null,
            doneOrder: null,
        },
        methods:{
            selectImage () {
                this.$refs.fileInput.click()
            },
            inputImage(e){

                var flag = 0
                if (this.imgExt.includes(e.target.files[0].name.split('.').pop())){
                    flag +=1
                }else{
                    alert('Please select Image format')
                }
                if ( (e.target.files[0].size) <= 2097152){
                    flag +=1
                }else{
                    alert('Image selected more than 2MB')
                }
                if(flag >=2){
                    this.categories.img_url = e.target.files[0]
                    this.img_preview = URL.createObjectURL(e.target.files[0])
                }

            },
            removeImgPreview(e){
                this.categories.img_url = null
                this.img_preview = null
            },
            showImage(img){
                if(img){
                    this.$swal({
                        imageUrl: img,
                        showConfirmButton: false,
                        padding: '0 1em',
                    })
                }
            },
        },
    }
</script>


<style >
    .X{
        left:50%
    }
    .swal2-popup{
        display: block !important;
    }
    .is-invalid{
        border-color: red
    }
    .cursor-zoom-in {
        cursor: -webkit-zoom-in;
        cursor: zoom-in;
    }
</style>
