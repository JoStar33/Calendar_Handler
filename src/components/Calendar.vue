<template>
    <div class="fill-height">
        <v-row align="center" class="fill-height" justify="center">
            <v-col cols="12" lg="8" md="10" sm="12" xl="7">
                <div class="mx-5">
                    <div class="text-center text-uppercase">
                        <h1>일정</h1>
                    </div>
                    <v-row>
                        <v-col cols="6">
                            <v-menu
                                :close-on-content-click="false"
                                :nudge-right="40"
                                :return-value.sync="start"
                                offset-y="offset-y"
                                ref="startMenu"
                                transition="scale-transition"
                                v-model="startMenu"
                                min-width="270px">
                                <template v-slot:activator="{ on }">
                                    <v-text-field
                                        dense="dense"
                                        hide-details="hide-details"
                                        label="Start Date"
                                        outlined="outlined"
                                        prepend-icon="mdi-calendar"
                                        readonly="readonly"
                                        v-model="start"
                                        v-on="on"></v-text-field>
                                </template>

                                <v-date-picker no-title="no-title" scrollable="scrollable" v-model="start">
                                    <v-spacer></v-spacer>
                                    <v-btn @click="startMenu = false" color="primary" dark="dark" text="text">Cancel</v-btn>
                                    <v-btn
                                        @click="$refs.startMenu.save(start)"
                                        color="primary"
                                        dark="dark"
                                        text="text">OK</v-btn>
                                </v-date-picker>
                            </v-menu>
                        </v-col>
                        <v-col cols="6">
                            <v-select
                                :items="typeOptions"
                                class="my-auto"
                                dense="dense"
                                hide-details="hide-details"
                                label="Type"
                                outlined="outlined"
                                v-model="type"></v-select>
                        </v-col>
                    </v-row>
                    <div class="text-center mb-3">
                        <v-btn
                            @click="$refs.calendar.prev()"
                            class="mr-auto"
                            color="white"
                            fab="fab"
                            outlined="outlined"
                            x-small="x-small">
                            <v-icon>mdi-chevron-left</v-icon>
                        </v-btn>
                        <h2 class="mx-5 d-inline-block">{{start | moment('YYYY MMMM')}}</h2>
                        <v-btn
                            @click="$refs.calendar.next()"
                            class="ml-auto"
                            color="white"
                            fab="fab"
                            outlined="outlined"
                            x-small="x-small">
                            <v-icon>mdi-chevron-right</v-icon>
                        </v-btn>
                    </div>
                    <v-sheet height="500">
                        <!--달력부분-->
                        <v-calendar
                            :event-color="getEventColor"
                            :events="events"
                            :event-overlap-threshold="30"
                            :start="start"
                            :type="type"
                            :event-more="false"
                            @click:date="openDialog"
                            @click:event="showEvent"
                            @click:more="moreWantEvent"
                            ref="calendar"
                            v-model="start"></v-calendar>
                    </v-sheet>
                    <div class="text-right mt-3 font-weight-bold ">
                        <v-btn
                            @click="open({ date: start })"
                            class="white--text"
                            color="indigo"
                            large="large">일정 추가
                        </v-btn>
                    </div>
                </div>
            </v-col>
        </v-row>
        <Dialog :dialog="true"/>
        <Detail :dialog="true"/>
    </div>
</template>

<script>
    import Dialog from "./Dialog";
    import Detail from "./Detail";

    export default {
        data: () => ({
            startMenu: false,
            start: '',
            type: 'month',
            typeOptions: [
                {
                    text: 'Day',
                    value: 'day'
                }, {
                    text: 'Week',
                    value: 'week'
                }, {
                    text: 'Month',
                    value: 'month'
                }
            ]
        }),
        components: {
            Detail,
            Dialog
        },
        methods: {
            openDialog(date) {
                this
                    .$store
                    .commit('OPEN_DIALOG', date)
            },
            showEvent({event}) {
                this
                    .$store
                    .commit('SHOW_EVENT_DETAIL', event);
            },
            moreWantEvent({date}) {
                this.start = date;
                this.type = 'day';
            },
            getEventColor(event) {
                return event.color;
            }
        },
        computed: {
            events() {
                return this.$store.state.calendar.events;
            }
        },
        created() {
            this.start = this
                .$moment()
                .format('YYYY-MM-DD');
        }
    }
</script>

<style scoped="scoped">
    .controls {
        position: relative;
    }
</style>