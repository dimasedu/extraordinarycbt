<template>
	<div class="row">
        <div class="col-lg-12">
            <div class="card">
                <div class="card-header">
                    <router-link :to="{ name: 'banksoal.soal', params: { banksoal_id: $route.params.banksoal_id } }" class="btn btn-light btn-sm mr-1">Kembali</router-link>
                    <button class="btn float-right btn-primary btn-sm mx-1" @click="print">Print</button>
                </div>
                <div class="card-body back" id="printSoal">
                	<div class="paper">
                		<table class="table table-sm">
							<tr v-for="(soal, index) in soals.data">
								<td>
									<table class="table-sm table-mx table-borderless">
										<tr>
											<td width="20px">{{ index+1 }}.</td>
											<td v-html="soal.pertanyaan"></td>
										</tr>
										<tr>
											<td colspan="2">
												<table class="table-sm">
													<tr v-for="(jawaban, index) in soal.jawabans">
														<td width="20px"></td>
														<td style="text-transform: uppercase;" :class="{ 'corect' : jawaban.correct == '1' }"> {{ index | charIndex }} ) <i v-show="jawaban.correct == '1'"  class="cil-star text-warning"></i> </td>
														<td v-html="jawaban.text_jawaban"></td>
													</tr>
												</table>
											</td>
										</tr>
									</table>
								</td>
							</tr>
						</table>
                	</div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
	import { mapActions, mapState, mapGetters } from 'vuex'
	export default {
		name: 'SoalBanksoal',
		created() {
			this.getAllSoalPaper()
		},
		computed: {
	        ...mapGetters(['isLoading']),
			...mapState('soal', {
	            soals: state => state.allSoals
			}),
			page: {
				get() {
					return this.$store.state.soal.page
				},
				set(val) {
					this.$store.commit('soal/SET_PAGE',val)
				}
			}
		},
		filters: {
			charIndex(i) {
				return String.fromCharCode(97 + i)
			}
		},
		methods: {
			...mapActions('soal',['getSoals','getAllSoal','removeSoal']),
			getAllSoalPaper() {
				this.getAllSoal({ banksoal_id: this.$route.params.banksoal_id })
	            .then(() => {
	                
	            })
			},
			print() {
	            this.$htmlToPaper('printSoal');
	        }
		}
	}
</script>
<style>
	@media print {
	    footer {page-break-after: always; }
		@page {
		  size: A4;
		  margin-bottom:60px;
		  margin-top:40px;
		  margin-left: 40px;
		  margin-right: 40px;	  
		}
	}
	.paper {
		max-width: 1140px;
		padding-right: 15px;
		padding-left: 15px;
		margin-right: auto;
		margin-left: auto;
		background: #fff;
		border: 4px double #ccc;
	}
	.back {
		background: #3c4b640d
	}
</style>