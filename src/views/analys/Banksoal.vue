<template>
    <div class="row">
        <div class="col-lg-12">
            <div class="card">
                <div class="card-header">
                   Analys banksoal
                </div>
                <div class="card-body">
                     <div class="row">
                        <div class="col-sm-5">
                            <h4 id="traffic" class="card-title mb-0">Analys banksoal</h4>
                            <div class="small text-muted">Pilih soal yang akan dilihat analysanya</div>
                        </div>
                    </div>
                    <br>
                    <div class="row">
                        <div class="col-md-3">
                            <b-form-group
                              label="Filter"
                              label-cols-sm="3"
                              label-align-sm="right"
                              label-size="sm"
                              label-for="filterInput"
                            >
                              <b-input-group size="sm">
                                <b-form-input
                                  v-model="search"
                                  type="search"
                                  id="filterInput"
                                  placeholder="Cari kode banksoal"
                                ></b-form-input>
                                <b-input-group-append>
                                  <b-button :disabled="!search" @click="search = ''">Clear</b-button>
                                </b-input-group-append>
                              </b-input-group>
                            </b-form-group>
                            <b-form-group
                              label="Per page"
                              label-cols-sm="6"
                              label-cols-md="4"
                              label-cols-lg="3"
                              label-align-sm="right"
                              label-size="sm"
                              label-for="perPageSelect"
                            >
                              <b-form-select
                                v-model="perPage"
                                id="perPageSelect"
                                size="sm"
                                :options="pageOptions"
                              ></b-form-select>
                            </b-form-group>
                        </div>
                    </div>
                    <template v-if="banksoals && typeof banksoals.data != 'undefined'">
                        <b-table striped hover bordered small :fields="fields" :items="banksoals.data" show-empty>
                           <template v-slot:cell(actions)="row">
                                <router-link :to="{ name: 'analys.banksoal_soal', params: {banksoal_id: row.item.id} }" class="btn btn-info btn-sm mr-1"><i class="cil-chart-pie"></i> Hasil analisa</router-link>
                            </template>
                        </b-table>
                        <div class="row">
                            <div class="col-md-6">
                                <p v-if="banksoals.data"><i class="fa fa-bars"></i> {{ banksoals.data.length }} item dari {{ banksoals.total }} total data</p>
                            </div>
                            <div class="col-md-6">
                                <div class="float-right">
                                    <b-pagination
                                        size="sm"
                                        v-model="page"
                                        :total-rows="banksoals.total"
                                        :per-page="banksoals.per_page"
                                        :disabled="isLoading"
                                        ></b-pagination>
                                </div>
                            </div>
                        </div>
                    </template>
                    <template v-else>
                        <div class="text-center text-light my-2">
                            <b-spinner small type="grow"></b-spinner>
                        </div>
                    </template>
                </div> 
            </div>
        </div>
    </div>
</template>
<script>
import { mapActions, mapState, mapGetters } from 'vuex'
import _ from 'lodash'

export default {
    name: 'DataBanksoal',
    created() {
        this.getBanksoals({ perPage: this.perPage, search: this.search }).then(() => { this.isBusy = false })
    },
    data() {
        return {
            fields: [
                { key: 'kode_banksoal', label: 'Kode banksoal'},
                { key: 'matpel.nama', label: 'Mata pelajaran'},
                { key: 'actions', label: 'Aksi' }
            ],
            perPage: 20,
            pageOptions: [20, 50, 100],
            search: '',
            selected: '',
            isBusy: true,
            update: 0
        }
    },
    computed: {
        ...mapGetters(['isLoading']),
        ...mapState(['errors']),
        ...mapState('banksoal', {
            banksoals: state => state.banksoals
        }),
        page: {
            get() {
                return this.$store.state.banksoal.page
            },
            set(val) {
                this.$store.commit('banksoal/SET_PAGE', val)
            }
        }
    },
    methods: {
        ...mapActions('banksoal', ['getBanksoals'])
    },
    watch: {
        page() {
            this.getBanksoals({ perPage: this.perPage, search: this.search })
        },
        search: _.debounce(function (value) {
            this.getBanksoals({ search: this.search, perPage: this.perPage })
        }, 500),
        perPage() {
            this.getBanksoals({ search: this.search, perPage: this.perPage })
        }
    },
}
</script>
