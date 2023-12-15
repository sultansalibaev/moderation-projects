<template>
    <div class="p-[20px]">
        <div class="flex items-center gap-[15px]">
            <input type="text" @input="inputProject" placeholder="Введите название либо ID-проекта" class="m-0 w-full rounded-[5px] border-none bg-white text-black ring-1 focus:outline-none !text-[14px] !px-[10px] leading-[1.3] transition-all h-[30px]">
            <VCheckboxList
                :min_width="180"
                :list="Object.values(status)"
                :trigger="'id'"
                :name="'name'"
                :list_modal="params_modal"
                :list_name="'Статус'"
                :modal_name="'status'"
                :set_list_modal="(name) => { params_modal = name }"
                :callback="(new_status) => { Object.keys(status).forEach(status_key => {
                    status[status_key].checked = new_status.includes(status_key)
                })}"
            />
            <v-pagination
                :selected_page="selected_page"
                :general_count="projects.length"
                :set_selected_page="set_selected_page"
            />
        </div>
        <ProjectsTable
            :projects="temp_projects"
            :project_info="project_info"
            :continents="continents"
            :block_continents="block_continents"
            @toggle-tags="toggle_tags"
            @toggle-place-ids="toggle_place_ids"
        />
    </div>
</template>
  
<script>
import axios from 'axios'
import '@/utils'
import ProjectsTable from '@/components/ProjectsTable.vue'
import VCheckboxList from '@/components/VCheckboxList.vue'
import VPagination from '@/components/VPagination.vue'

export default {
    name: 'App',
    components: {
        ProjectsTable,
        VCheckboxList,
        VPagination,
    },
    data() {
        return {
            params_modal: '',
            input_project: '',
            typingTimer: null,
            doneTypingInterval: 1000,
            projects: [],
            sorted_projects: [],
            project_info: {},
            continents: {},
            block_continents: {},
            status: {
                moderation: {
                    id: 'moderation',
                    name: 'на модераций',
                    checked: true,
                },
                click_process: {
                    id: 'click_process',
                    name: 'В очереди',
                    checked: true,
                },
                click_work: {
                    id: 'click_work',
                    name: 'проект в обработке',
                    checked: true,
                },
                done: {
                    id: 'done',
                    name: 'проект собран',
                    checked: true,
                },
            },
            selected_page: 0,
        }
    },
    methods: {
        set_selected_page(page) {
            this.selected_page = parseInt(page);
            this.update_project_info();
        },
        toggle_tags(project_id) {
            this.project_info[project_id].show_all_tags = !this.project_info[project_id].show_all_tags;
        },
        toggle_place_ids(project_id) {
            this.project_info[project_id].show_all_place_ids = !this.project_info[project_id].show_all_place_ids;
        },
        getModerationProjects() {
            return axios.get('/ru/projectadminedit/get-moderation-projects')
                .then(response => {
                    let data = response.data
                    console.log('moderation-projects data', data)
                    this.continents = data.continents.reduce(
                        (obj, continent) => ({
                            ...obj, [continent.id]: continent
                        }), {}
                    );
                    this.block_continents = data.block_continents.reduce(
                        (obj, continent) => ({
                            ...obj, [continent.country_id]: continent
                        }), {}
                    );
                    this.sorted_projects = this.projects = data.projects;
                    this.update_project_info();
                })
                .catch(error => {
                    console.log(error);
                })
        },
        inputProject({ target: { value } }) {

            let old_input_project = this.input_project;
            this.input_project = value;

            clearTimeout(this.typingTimer);
			this.typingTimer = setTimeout(() => {
                this.sort_projects(old_input_project)
            }, this.doneTypingInterval);
        },
        sort_projects(old_input_project) {

            if (this.input_project == old_input_project) return;

            if (this.input_project == '') {
                this.sorted_projects = this.projects.slice();
                this.update_project_info();
                return;
            }

            let temp_sorted_projects;

            if (this.input_project.startsWith(old_input_project)) {
                temp_sorted_projects = this.sorted_projects.slice();
            }
            else {
                temp_sorted_projects = this.projects.slice();
            }

            let param = 'id';
            if (Number.isNaN(Number(this.input_project))) param = 'name';

            console.log('temp_sorted_projects', temp_sorted_projects);

            this.sorted_projects = temp_sorted_projects
                .filter(item => (item?.[param] || '').lowerIncludes(this.input_project))
                .sort((a,b) => {
                    const a_index = a?.[param]?.indexOf(this.input_project);
                    const b_index = b?.[param]?.indexOf(this.input_project);
                    return a_index < b_index ? -1 : 0
                });

            console.log('this.sorted_projects', this.sorted_projects);
            this.update_project_info();
        },
        getTags(tags) {
            return tags
                .replaceAll('AND', ', ')
                .replaceAll('OR', ', ')
                .replaceAll('ANT', ', ')
                .replaceAll('(', '')
                .replaceAll(')', '')
                .replaceAll(', , , ', ', ')
                .replaceAll(', , ', ', ')
                .split(', ');
        },
        update_project_info() {
            this.project_info = this.temp_projects.reduce(
                (obj, project) => ({...obj, [project.id]: {
                    show_all_tags: false,
                    show_all_place_ids: false,
                    place_ids: project.place_id.split(','),
                    tags: this.getTags(project.query)
                }})
            , {})
        },
    },
    computed: {
        temp_projects() {
            return this.sorted_projects.slice(this.selected_page * 20, (this.selected_page * 20) + 20).filter(project => this.status[project.status] == undefined || this.status[project.status]?.checked)
        },
    },
    created() {
        this.getModerationProjects();
    },
}
</script>
  
<style>



</style>
  