<template>
    <div class="container" v-if='!close'>
        <div class="grid place-items-center pb-10">
            <table class="table">
                <!-- head -->
                <thead>
                    <tr>
                        <th>#</th>
                        <th class="text-center">Files Name</th>
                        <th>Action</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(item, index) in displayedItems" :key="index" :item="item">
                        <th>{{ index + 1 }}</th>
                        <th>{{ item.name }}</th>
                        <th>
                            <button class="btn btn-outline btn-success btn-sm mr-2"
                                @click="openReportTable(item.id)">View</button>
                            <!-- <button class="btn btn-outline btn-warning btn-sm">Print PDF</button> -->
                        </th>
                    </tr>
                </tbody>
            </table>
            <div class="flex flex-row gap-4">
                <button type="button" :class="['btn btn-primary  btn-sm mr-0.1', { isActive: page === currentPage }]"
                    v-for="page in pages" :key="page" @click="changePage(page)">
                    {{ page }}
                </button>
            </div>
        </div>
    </div>
</template>

<script  lang="ts">
import { ref, computed, watch } from 'vue';
export default {
    props: {
        items: {
            type: Array<any>,
            required: true
        },
        itemsPerPage: {
            type: Number,
            default: 7
        },
    },
    methods: {
        openReportTable(report_id: string) {
            this.$router.push({ name: 'dcv_healths_table', params: { id: report_id } });
        }
    },
    setup(props) {
        let close = false
        const currentPage = ref(1);

        const pageCount = computed(() =>
            Math.ceil(props.items.length / props.itemsPerPage)
        );

        const pages = computed(() => {
            return Array.from({ length: pageCount.value }, (_, i) => i + 1);
        });

        const displayedItems = computed(() => {
            const startIndex = (currentPage.value - 1) * props.itemsPerPage;
            const endIndex = startIndex + props.itemsPerPage;
            return props.items.slice(startIndex, endIndex);
        });

        function changePage(pageNumber: number) {
            currentPage.value = pageNumber;
        }

        watch(currentPage, () => {
            //If you want the window to scroll up on page change
            //window.scrollTo({
            //     top: 0,
            //     behavior: 'smooth'
            // });
        });

        return {
            currentPage,
            pageCount,
            pages,
            displayedItems,
            changePage,
            close
        };
    },
    beforeUnmount() {
        this.close = true;
    }
}
</script>