<template>
    <div>
        <div class="mt-4">
            <file-pond
                name="image"
                ref="pond"
                label-idle="Click to choose image, or drag here ..."
                @init="filepondInitialized"
                accepted-file-types="image/jpg,jpeg,png"
                @processfile="handleProcessFile"
            />
        </div>
        <div class="mt-8 mb-24">
            <h3 class="text-2xl font-medium text-center">
                Image Gallery
            </h3>
            <div class="grid grid-cols-3 gap-2 justify-evenly mt-4">
                <div v-for="(image, index) in images" :key="index">
                    <img :src="`/storage/images/${image}`"/>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import vueFilePond, { setOptions } from "vue-filepond";
import "filepond/dist/filepond.min.css";
import FilePondPluginFileValidateType from "filepond-plugin-file-validate-type";

setOptions({
    server: {
        process: {
            url: './upload',
            headers: {
                'X-CSRF-TOKEN': document.head.querySelector('meta[name="csrf_token"]').content
            },
            methods: 'POST'
        }
    }
})

const FilePond = vueFilePond(
    FilePondPluginFileValidateType
);

export default {
    components: {
        FilePond
    },
    data(){
        return {
            images: []
        }
    },
    mounted() {
        axios.get('/images')
            .then(response => {
                this.images = response.data
            })
            .catch(error => console.log(error))
    },
    methods: {
        filepondInitialized() {
            console.log('is ready', this.$refs.pond)
        },
        handleProcessFile(error, file) {
            if (error) {
                console.error(error)
                return;
            }

            this.images.unshift(file.serverId)
        }
    },
}
</script>