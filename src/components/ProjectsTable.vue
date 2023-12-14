<template>
    <div class="col-lg-12 p-0 m-t-md">
        <div class="b-sh p-20 !pr-[10px]" id="imas-project-table" :style="'height: calc(100vh - 150px);'">
            <div class="table-responsive b-none h-100 scrollbar !pr-[10px]">
                <table class="table table-hover issue-tracker" id="miyazaki">
                    <thead>
                        <tr>
                            <th class="project_visible">Видимость</th>
                            <th class="project_title">Название проекта</th>
                            <th class="project_tags">Ключевые слова</th>
                            <th class="project_period">Период сбора</th>
                            <th class="project_place">Информация поиска</th>
                            <th class="project_count t-a-c">Статус</th>
                        </tr>
                    </thead>
                    <tbody class="imas-sort-table ui-sortable">
                        <tr :id="project.id" class="ui-sortable-handle" v-for="project in projects" :key="project.id" @click.stop="editProject(project.id)">
                            <td class="project-title project_visible" data-th="Видимость">
                                <div class="switch f-l">
                                    <div class="onoffswitch" id="visible_click">
                                        <input type="checkbox" checked="" class="onoffswitch-checkbox"
                                            :id="`example_${project.id}`" :value="project.id">
                                        <label class="onoffswitch-label" :for="`example_${project.id}`">
                                            <span class="onoffswitch-inner"></span>
                                            <span class="onoffswitch-switch"></span>
                                        </label>
                                    </div>
                                </div>
                            </td>
                            <td class="project-title project_title" data-th="Название проекта">
                                <div class="calc_width">
                                    <div class="btn-group">
                                        <a class="collapse-link dropdown-toggle more_labels b-sh-n f-l"
                                            data-toggle="dropdown" aria-expanded="false">
                                            {{ project.name }}<span class="caret"></span>
                                        </a>
                                        <ul class="dropdown-menu" style="min-width: 230px;">
                                            <li>
                                                <a :href="`/ru/tape?id=${project.id}&t=all&p=1`" target="_blank">
                                                    <i class="fa fa-newspaper-o p-r-5"></i> Лента </a>
                                            </li>

                                            <li>
                                                <a :href="`/ru/tapeprocessing?id=${project.id}&t=all&p=1`" target="_blank">
                                                    <i class="fa fa-edit p-r-5"></i> Страница обработки </a>
                                            </li>

                                            <li>
                                                <a :href="`/ru/project-search?id=${project.id}&t=all&p=1`" target="_blank">
                                                    <i class="fa fa-search p-r-5"></i> Поиск </a>
                                            </li>

                                            <li>
                                                <a :href="`/ru/analytics?id=${project.id}`" target="_blank">
                                                    <i class="fa fa-line-chart p-r-5"></i> Аналитика </a>
                                            </li>
                                            <li>
                                                <a :href="`/ru/resource?id=${project.id}`" target="_blank">
                                                    <i class="fa fa-share-alt p-r-5"></i> Источники </a>
                                            </li>
                                            <li>
                                                <a :href="`/ru/notifications?id=${project.id}`" target="_blank">
                                                    <i class="fa fa-envelope p-r-5"></i> Уведомление </a>
                                            </li>

                                            <li>
                                                <a :href="`/ru/settings?id=${project.id}`" target="_blank">
                                                    <i class="fa fa-cogs p-r-5"></i> Настройки </a>
                                            </li>

                                            <li>
                                                <a :href="`/ru/settings/copying?id=${project.id}`" target="_blank">
                                                    <i class="fa fa-clone p-r-5"></i> Копировать проект </a>
                                            </li>

                                            <li class="divider"></li>
                                            <li>
                                                <a>
                                                    <span>Автообновление</span>
                                                    <div class="switch f-r">
                                                        <div class="onoffswitch" id="appender_click">
                                                            <input type="checkbox" checked="" class="onoffswitch-checkbox"
                                                                :id="`example1_${project.id}`" :value="project.id">
                                                            <label class="onoffswitch-label"
                                                                :for="`example1_${project.id}`">
                                                                <span class="onoffswitch-inner"></span>
                                                                <span class="onoffswitch-switch"></span>
                                                            </label>
                                                        </div>
                                                    </div>
                                                </a>
                                            </li>

                                            <li>
                                                <!-- onclick="delete_project('Теңізшевройл', 2486);" -->
                                                <a><i class="fa fa-trash p-r-10"></i>Удалить проект </a>
                                            </li>

                                        </ul>
                                    </div>
                                    <br>
                                    <small>Дата создания: {{ project.create_date }}</small>
                                </div>
                            </td>

                            <td class="project-title project-manager project_tags" data-th="Ключевые слова">
                                <div class="calc_width">
                                    <ul class="tag-list" :id="`tag-list-${project.id}`" style="padding: 0">
                                        <li
                                            :key="tag"
                                            v-for="tag in project_info[project.id].tags.slice(0, (
                                            project_info[project.id].show_all_tags ? undefined : 10
                                        ))">
                                            <a :title="project.name"><i class="fa fa-tag p-r-5"></i>{{ tag }}</a>
                                        </li>

                                        <li v-if="project_info[project.id].tags.length > 10">
                                            <a
                                                class="bg-white label-and-more"
                                                @click.stop="$emit('toggle-tags', project.id)"
                                            >
                                                {{!project_info[project.id].show_all_tags ? `+ еще ${project_info[project.id].tags.length - 10}` : 'скрыть' }}
                                            </a>
                                        </li>
                                    </ul>
                                </div>
                            </td>

                            <td class="project-completion project_period" data-th="Период сбора">
                                <div class="calc_width">
                                    <div class="block m-b-8"><i class="fa fa-calendar p-r-5"></i>{{ project.s_date }} - {{
                                        project.f_date }}
                                    </div>
                                    <div class="block">
                                        <small>Автообновление:</small>
                                        <span v-if="[2, 3, 777].includes(+project.appender_status)"
                                            class="label label-primary">включен</span>
                                        <span v-else class="label label-danger">отключен</span>
                                    </div>
                                </div>
                            </td>

                            <td class="project-completion project_place" data-th="Информация поиска">
                                <div class="calc_width">
                                    <div class="block m-b-8">
                                        <small>Область поиска:</small>
                                        <span
                                            v-if="[project.categories_id, project.social_type_id].every(cat => cat == 'all')"
                                            class="label label-imas-smi">СМИ</span> + <span
                                            class="label label-imas-social">Соцсети</span>
                                        <span v-if="project.categories_id != 'all'"
                                            class="label label-imas-social">Соцсети</span>
                                        <span v-if="project.social_type_id != 'all'" class="label label-imas-smi">СМИ</span>
                                    </div>

                                    <div class="block">
                                        <small>География поиска:</small>


                                        <span v-if="project.place == 0" class=""><i class="fa fa-globe p-r-5"></i>Мир</span>

                                        <template v-else-if="project.place == 1">
                                            <span class=""><i class="fa fa-globe p-r-5"></i>Континенты</span>
                                            <div class="btn-group">
                                                <a class="collapse-link dropdown-toggle more_labels b-sh-n"
                                                    data-toggle="dropdown" aria-expanded="false">
                                                    <span class="caret"></span>
                                                </a>

                                                <ul class="dropdown-menu block_more_labels">
                                                    <li class="f-l" :key="place_id" v-for="place_id in project_info[project.id].place_ids.slice(0, (
                                                        project_info[project.id].show_all_place_ids ? undefined : 5
                                                    ))">
                                                        <a class="label label-imas">
                                                            {{ continents[place_id]?.name ?? `Континент ${place_id} не
                                                            найден` }}
                                                        </a>
                                                    </li>
                                                    <li class="f-l" v-if="project_info[project.id].place_ids.length > 5">
                                                        <a class="label label-imas label-and-more"
                                                            @click.stop="$emit('toggle-place-ids', project.id)">
                                                            {{ !project_info[project.id].show_all_place_ids ? `+ еще
                                                            ${project_info[project.id].place_ids.length - 5}` : 'скрыть' }}
                                                        </a>
                                                    </li>
                                                </ul>
                                            </div>
                                        </template>

                                        <template v-else-if="project.place == 2 && project.place_id != 57">
                                            <span v-if="(
                                                project_info[project.id].place_ids.length >= 217 &&
                                                project_info[project.id].place_ids
                                                    .map(place_id => (+place_id))
                                                    .includes(50)
                                            )" class=""><i class="fa fa-globe p-r-5"></i>Мир - кроме Казахстана</span>
                                            <template v-else>
                                                <span class=""><i class="fa fa-globe p-r-5"></i>Страны</span>
                                                <div class="btn-group">
                                                    <a class="collapse-link dropdown-toggle more_labels b-sh-n"
                                                        data-toggle="dropdown" aria-expanded="false">
                                                        <span class="caret"></span>
                                                    </a>

                                                    <ul class="dropdown-menu block_more_labels">
                                                        <li class="f-l" :key="place_id"
                                                            v-for="place_id in project_info[project.id].place_ids.slice(0, 8)">
                                                            <a class="label label-imas">
                                                                <img class="inline-block flag_country_img m-r-2"
                                                                    :src="`/media/img/country/${block_continents[place_id].hc_key || 'unknown'}.png`">
                                                                {{ block_continents[place_id]?.country_name ?? `Страна
                                                                ${place_id} не найдена` }}
                                                            </a>
                                                        </li>
                                                        <li class="f-l"
                                                            v-if="project_info[project.id].place_ids.length > 8">
                                                            <a class="label label-imas label-and-more">
                                                                + еще {{ project_info[project.id].place_ids.length - 8 }}
                                                            </a>
                                                        </li>
                                                    </ul>
                                                </div>
                                            </template>
                                        </template>


                                        <span v-if="project.place == 2 && project.place_id == 57"><img
                                                class="inline-block flag_country_img m-r-xs"
                                                src="/media/img/country/kz.png">Казахстан</span>



                                    </div>
                                </div>
                            </td>

                            <td class="project-completion project_count t-a-c !whitespace-nowrap" data-th="Статус">
                                {{ projectStatus[project.status] ?? project.status }}
                                <!-- <button type="button" class="btn btn-primary" @click.stop="editProject(project.id)">
                                    <span class="f-s-16 f-w-400">Изменить</span>
                                </button> -->
                            </td>
                        </tr>


                        <!-- <script class="ui-sortable-handle" style="">
                            function restoreProject(p_id){
                                $.ajax({
                                    url: "/ru/settingsqueries/restore-project",
                                    data : {p_id: p_id},
                                    type : "GET",
                                    success: function(data){
                                        swal({
                                            title: "Восстановлен!",
                                            text: "Ваш проект успешно восстановлен.",
                                            type: "success"
                                        }, function(){
                                            window.location.href = '/basic';
                                        });
                                    }
                                })
                            }
                        </script> -->
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</template>

<script>

export default {
    props: {
        projects: {
            type: Array,
            required: true,
        },
        project_info: {
            type: Object,
            required: true,
        },
        continents: {
            type: Object,
            required: true,
        },
        block_continents: {
            type: Object,
            required: true,
        },
    },
    data: () => ({
        projectStatus: {
            click_process: 'В очереди',
            click_work: 'проект в обработке',
            done: 'проект собран'
        }
    }),
    methods: {
        editProject(p_id) {
            window.open(`/ru/projectadminedit?p_id=${p_id}`, '_blank');
        },
    },
}

</script>

<style scoped>

/* #app tbody tr td, */
#app thead tr th {
    cursor: default !important;
}

/* #app tbody tr td:last-child .btn-primary { */
#app tbody tr td {
    cursor: pointer !important;
}

</style>