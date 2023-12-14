<template>
    <div class="inline-flex relative" :style="{
        minWidth: `${min_width}px`
    }">
        <div @click.stop="toggle_prompt_list_modal()"
            class="inline-flex justify-between items-center cursor-pointer select-title"
            style="background: white;border: 1px solid #3b5998;padding: 0;overflow: hidden;min-width: 100%;height: 28px;">
            <span class="flex items-center w-full h-full font-semibold whitespace-nowrap select-none"
                style="padding-left: 8px;letter-spacing: .8px;">{{ list_name }}</span>

            <i class="fa-solid fa-angle-down transition-all" style="margin-left:5px;margin-right: 6px;height:12px;"
                @click.stop="toggle_prompt_list_modal()" :class="{
                    'rotate-x-180': list_modal == modal_name
                }"></i> 
        </div>
        <div class="absolute top-full right-0 left-0 transition-all select-options" :style="{
            height: list_modal == modal_name ? '' : '0px',
        }">
            <div class="flex flex-col select-none select-options-styles scrollbar" style="max-height: 200px;">
                <div class="select-option" v-for="item in list" :key="item[trigger]"
                    :title="`${item[trigger]} - ${item[name]}`"
                    @click="(event) => {
                        console.log('checkbox ctrl event', event.ctrlKey, event);

                        if (event.ctrlKey) {
                            list.forEach(item => {item.checked = false})
                        }
                        
                        item.checked = !item?.checked;
                        callback(checked_list);
                    }"
                >
                    <input type="checkbox" class="pointer-events-none" style="margin: 1.5px 5px 0 0;" :checked="item?.checked">
                    <span class="select-none">{{ item[name] }}</span>
                </div>
            </div>
        </div>
    </div>
</template>

<script>


export default {
    methods: {

        toggle_prompt_list_modal() {
            this.set_list_modal(this.list_modal != this.modal_name ? this.modal_name : '');
        },
    },
    props: {
        list: {
            type: Array,
            required: true,
        },
        name: {
            type: String,
            required: true,
        },
        trigger: {
            type: String,
            required: true,
        },
        list_modal: {
            type: Boolean,
            required: true,
        },
        list_name: {
            type: String,
            required: true,
        },
        modal_name: {
            type: String,
            required: true,
        },
        set_list_modal: {
            type: Function,
            required: true,
        },
        checked_all: {
            type: Boolean,
            required: false,
            default: false,
        },
        callback: {
            type: Function,
            required: true,
        },
        min_width: {
            type: Number,
            required: false,
            default: 160
        },
    },
    data() {
        let default_input = this.defaultInput || '';
        return {
            input: default_input,
        }
    },
    computed: {
        checked_list() {
            return this.list.filter(item => item?.checked).map(item => item[this.trigger])
        },
    },
    created() {
        this.list.forEach(item => { item.checked = true })
    },
}

</script>

<style>
.rotate-x-180 {
    transform: rotateX(180deg);
}

.select-option {
    padding: 6px 7px;
    cursor: pointer;
    height: 27px;
    white-space: nowrap;
    transition: .15s;

    letter-spacing: .8px;
    display: flex;
    align-items: center;
    margin: 0;
}

/* .select-option.active,
.select-option:hover {
    background: rgb(59, 89, 152);
    color: white;
} */

.select-option:not(.select-option:last-child) {
    border-bottom: 1px solid rgb(59, 89, 152);
}

.select-options {
    z-index: 1;
    overflow-y: clip;
}

.select-options-styles {
    font-size: 13px;
    background: white;
    border-radius: 4px;
    border: 1px solid rgb(59, 89, 152);
    margin-top: 5px;
}

.select-title {
    background: rgb(59, 89, 152);
    height: 27px;
    padding: 0px 8px 2px;
    border-radius: 4px;
    font-size: 13px;
    width: 150px;
}

.select-input-error {
    border-color: red !important;
}
</style>