<template>
 <div class="container">
		<!-- file upload - all-->
		<div class="row mt-5">
			<div class="col-md-12 round text-center">

				<!-- file upload - step 1 -->
				<div class="row">
					<div class="offset-0 col-12 offset-md-2 col-md-8">
						<div id="chooseDoc">
							<p class="mt-5"><i v-if="!url" class="fas fa-file-upload opacity_30" style="font-size: 80px; margin-bottom: 40px;"></i><br>{{filename}}<br></p>
							<input type="file" ref="selectedFile" id="selectedFile" style="display: none;" v-on:change="handleFileChange($event)" />
							<button class="btn btn-primary mt-3 mb-3 mx-auto" accept="image/x-png,image/gif,image/jpeg" name="uploadBtn" onclick="document.getElementById('selectedFile').click();">Fahrzeugschein auswählen</button>
						</div>

						<div id="preview p-0">
							<img class="img-fluid scan" v-if="url" :src="url" />
						</div>
					</div>
				</div>
				
				<div class="row">
					<div class="offset-0 col-12 offset-md-2 col-md-8">
						<div v-if="url">
							<input type="text" class="form-control mt-2" v-model="accessKey" id="accessKey" aria-label="accessKey" placeholder="Access-Key" aria-describedby="accessKey" v-on:change="onAccessKeyChanged()">
		
							<button type="button" class="btn btn-primary mt-4 mr-2 mb-3" name="scan" v-on:click="scan()">Digitalisieren</button>
							<button type="button" class="btn btn-secondary mt-4 mb-3" name="cancel" v-on:click="cancel()">Abbrechen</button>
						</div>
					</div>
				</div>

				<div v-if="error" class="row">
					<div class="offset-0 col-12 offset-md-2 col-md-8">
						<div class="alert alert-danger" role="alert">
							{{ errorMsg }}
						</div>
					</div>
				</div>
				<div v-if="loading" class="spinner-grow mb-5 mt-3" role="status">
					<span class="sr-only">Loading...</span>
				</div>
			</div>
		</div>
		<!-- file upload end -->

		<!-- resultfields - start-->
		<div v-if="showResults" class="row mt-5">
			<div class="col-md-12 round p-3 p-md-5">
				<div class="tab-content">
					<div class="tab-pane active" id="scanresult_01" role="tabpanel">

						<!-- 1 Page -->
						<img src="@/assets/img/icon_scanresult_page_1.svg" height="30px" alt="Seite 1" class="mt-3">
						<h3 class="mt-3">Seite 1</h3>

						<!-- headline row-->
						<div class="d-md-block">
							<div class="row mt-4">
								<div class="col-3">
									<p class="mb-3 pb-1"><strong>Feldbezeichnung</strong></p>
								</div>
								<div class="col-4">
									<p><strong>Digitalisiert</strong></p>
								</div>
								<div class="col-12 col-md-4 offset-md-1">
									<p><strong>Ausschnitt</strong></p>
								</div>
							</div>
						</div>

						<!-- scan detail rows-->
						<div class="row" v-if="registrationNumber !== '' && registrationNumber !== null || registrationNumber_img !== '' && registrationNumber_img !== null">
							<div class="col-12 col-md-3">
								<p class="field_name mb-0">Kennzeichen</p>
							</div>
							<div class="col-12 col-md-4">
								<div class="input-group mb-2">
									<input type="text" class="form-control" id="registrationNumber" v-model="registrationNumber" aria-label="plate" aria-describedby="plate">
									<div class="input-group-append">
										<button class="btn btn-outline-secondary" type="button" v-on:click="copy('registrationNumber')"><i class="fas fa-copy"></i></button>
									</div>
								</div>
							</div>
							<div class="col-12 col-md-4 offset-md-1">
								<img v-if="registrationNumber_img !== '' && registrationNumber_img !== null" v-bind:src="registrationNumber_img" class="img-fluid cut_border">
							</div>
						</div>

						<div class="row" v-if="firstname !== '' && firstname !== null || firstname_img !== '' && firstname_img !== null">
							<div class="col-12 col-md-3">
								<p class="field_name mb-0">Vorname</p>
							</div>
							<div class="col-12 col-md-4">
								<div class="input-group mb-2">
									<input type="text" class="form-control" v-model="firstname" id="firstname" aria-label="plate" aria-describedby="firstname">
									<div class="input-group-append">
										<button class="btn btn-outline-secondary" type="button" v-on:click="copy('firstname')"><i class="fas fa-copy"></i></button>
									</div>
								</div>
							</div>
							<div class="col-12 col-md-4 offset-md-1">
								<img v-if="firstname_img !== '' && firstname_img !== null" v-bind:src="firstname_img" class="img-fluid cut_border">
							</div>
						</div>

						<div class="row" v-if="name1 !== '' && name1 !== null || name1_img !== '' && name1_img !== null">
							<div class="col-12 col-md-3">
								<p class="field_name mb-0">Name</p>
							</div>
							<div class="col-12 col-md-4">
								<div class="input-group mb-2">
									<input type="text" class="form-control" v-model="name1" id="name1" aria-label="plate" aria-describedby="name">
									<div class="input-group-append">
										<button class="btn btn-outline-secondary" type="button" v-on:click="copy('name1')"><i class="fas fa-copy"></i></button>
									</div>
								</div>
							</div>
							<div class="col-12 col-md-4 offset-md-1">
								<img v-if="name1_img !== '' && name1_img !== null" v-bind:src="name1_img" class="img-fluid cut_border">
							</div>
						</div>

						<div v-if="name2 !== '' && name2 !== null || name2_img !== '' && name2_img !== null" class="row">
							<div class="col-12 col-md-3">
								<p class="field_name mb-0"></p>
							</div>
							<div class="col-12 col-md-4">
								<div class="input-group mb-2">
									<input type="text" class="form-control" v-model="name2" id="name2" aria-label="plate" aria-describedby="name">
									<div class="input-group-append">
										<button class="btn btn-outline-secondary" type="button" v-on:click="copy('name2')"><i class="fas fa-copy"></i></button>
									</div>
								</div>
							</div>
							<div class="col-12 col-md-4 offset-md-1">
								<img v-if="name2_img !== '' && name2_img !== null" v-bind:src="name2_img" class="img-fluid cut_border">
							</div>
						</div>

						<div class="row" v-if="address1 !== '' && address1 !== null || address2 !== '' && address2 !== null || address1_img !== '' && address1_img !== null || address2_img !== '' && address2_img !== null">
							<div class="col-12 col-md-3">
								<p class="field_name mb-0">Anschrift</p>
							</div>
							<div class="col-12 col-md-4">
								<div class="input-group mb-2" v-if="address1_img !== '' && address1_img !== null">
									<input type="text" class="form-control" v-model="address1" id="address1" aria-label="plate" aria-describedby="adress_street">
									<div class="input-group-append">
										<button class="btn btn-outline-secondary" type="button" v-on:click="copy('address1')"><i class="fas fa-copy"></i></button>
									</div>
								</div>
								<div class="input-group mb-2" v-if="address2_img !== '' && address2_img !== null">
									<input type="text" class="form-control" v-model="address2" id="address2" aria-label="plate" aria-describedby="adress_city">
									<div class="input-group-append">
										<button class="btn btn-outline-secondary" type="button" v-on:click="copy('address2')"><i class="fas fa-copy"></i></button>
									</div>
								</div>
							</div>
							<div class="col-12 col-md-4 offset-md-1">
								<img v-if="address1_img !== '' && address1_img !== null" v-bind:src="address1_img" class="img-fluid cut_border">
								<img v-if="address2_img !== '' && address2_img !== null" v-bind:src="address2_img" class="img-fluid cut_border">
							</div>
						</div>

						<hr>

						<!-- 2 Page -->
						<img src="@/assets/img/icon_scanresult_page_2.svg" height="30px" alt="Seite 1" class="mt-3">
						<h3 class="mt-3">Seite 2</h3>

						<!-- headline row-->
						<div class="d-md-block">
							<div class="row mt-4">
								<div class="col-3">
									<p class="mb-3 pb-1"><strong>Feldbezeichnung</strong></p>
								</div>
								<div class="col-4">
									<p><strong>Digitalisiert</strong></p>
								</div>
								<div class="col-12 col-md-4 offset-md-1">
									<p><strong>Ausschnitt</strong></p>
								</div>
							</div>
						</div>

						<!-- scan detail rows-->
						<div class="row" v-if="ez !== '' && ez !== null || ez_img !== '' && ez_img !== null">
							<div class="col-12 col-md-3">
								<p class="field_name mb-0">Erstzulassung</p>
							</div>
							<div class="col-12 col-md-4">
								<div class="input-group mb-2">
									<input type="text" class="form-control" v-model="ez" id="ez" aria-label="ez" aria-describedby="ez">
									<div class="input-group-append">
										<button class="btn btn-outline-secondary" type="button" v-on:click="copy('ez')"><i class="fas fa-copy"></i></button>
									</div>
								</div>
							</div>
							<div class="col-12 col-md-4 offset-md-1">
								<img v-if="ez_img !== '' && ez_img !== null" v-bind:src="ez_img" alt="" class="img-fluid cut_border">
							</div>
						</div>

						<div class="row" v-if="hsn !== '' && hsn !== null || hsn_img !== '' && hsn_img !== null">
							<div class="col-12 col-md-3">
								<p class="field_name mb-0">HSN</p>
							</div>
							<div class="col-12 col-md-4">
								<div class="input-group mb-2">
									<input type="text" class="form-control" v-model="hsn" id="hsn" aria-label="hsn" aria-describedby="hsn">
									<div class="input-group-append">
										<button class="btn btn-outline-secondary" type="button" v-on:click="copy('hsn')"><i class="fas fa-copy"></i></button>
									</div>
								</div>
							</div>
							<div class="col-12 col-md-4 offset-md-1">
								<img v-if="hsn_img !== '' && hsn_img !== null" v-bind:src="hsn_img" class="img-fluid cut_border">
							</div>
						</div>

						<div class="row" v-if="field_2_2 !== '' && field_2_2 !== null || field_2_2_img !== '' && field_2_2_img !== null">
							<div class="col-12 col-md-3">
								<p class="field_name mb-0">Feld 2.2</p>
							</div>
							<div class="col-12 col-md-4">
								<div class="input-group mb-2">
									<input type="text" class="form-control" v-model="field_2_2" id="field_2_2" aria-label="field_2_2" aria-describedby="tsn">
									<div class="input-group-append">
										<button class="btn btn-outline-secondary" type="button" v-on:click="copy('field_2_2')"><i class="fas fa-copy"></i></button>
									</div>
								</div>
							</div>
							<div class="col-12 col-md-4 offset-md-1">
								<img v-if="field_2_2_img !== '' && field_2_2_img !== null" v-bind:src="field_2_2_img" class="img-fluid cut_border">
							</div>
						</div>

						<div class="row" v-if="vin !== '' && vin !== null || vin_img !== '' && vin_img !== null">
							<div class="col-12 col-md-3">
								<p class="field_name mb-0">VIN</p>
							</div>
							<div class="col-12 col-md-4">
								<div class="input-group mb-2">
									<input type="text" class="form-control" v-model="vin" id="vin" aria-label="vin" aria-describedby="vin">
									<div class="input-group-append">
										<button class="btn btn-outline-secondary" type="button" v-on:click="copy('vin')"><i class="fas fa-copy"></i></button>
									</div>
								</div>
							</div>
							<div class="col-12 col-md-4 offset-md-1">
								<img v-if="vin_img !== '' && vin_img !== null" v-bind:src="vin_img" class="img-fluid cut_border">
							</div>
						</div>

						<div class="row" v-if="d3 !== '' && d3 !== null || d3_img !== '' && d3_img !== null">
							<div class="col-12 col-md-3">
								<p class="field_name mb-0">D3</p>
							</div>
							<div class="col-12 col-md-4">
								<div class="input-group mb-2">
									<input type="text" class="form-control" v-model="d3" id="d3" aria-label="model" aria-describedby="model">
									<div class="input-group-append">
										<button class="btn btn-outline-secondary" type="button" v-on:click="copy('d3')"><i class="fas fa-copy"></i></button>
									</div>
								</div>
							</div>
							<div class="col-12 col-md-4 offset-md-1">
								<img v-if="d3_img !== '' && d3_img !== null" v-bind:src="d3_img" class="img-fluid cut_border">
							</div>
						</div>

					</div>
					<div class="tab-pane" id="scanresult_02" role="tabpanel">.2..</div>
					<div class="tab-pane" id="scanresult_03" role="tabpanel">.3..</div>
				</div>
			</div>
		</div>
		<!-- resultfields - end-->
 </div>
</template>

<script>
import axios from 'axios';
export default {
	name: 'Scanner',
	data: () => ({
		url: null,
		files: null,
		filename: 'Kein Dokument ausgewählt.',
		loading: false,
		showResults: false,
		ez: null,
		ez_img: null,
		hsn: null,
		hsn_img: null,
		field_2_2: null,
		field_2_2_img: null,
		vin: null,
		vin_img: null,
		d3: null,
		d3_img: null,
		registrationNumber: null,
		registrationNumber_img: null,
		name1: null,
		name1_img: null,
		name2: null,
		name2_img: null,
		firstname: null,
		firstname_img: null,
		address1: null,
		address1_img: null,
		address2: null,
		address2_img: null,
		error: false,
		errorMsg: '',
		accessKey: ''
	}),
	methods: {
		resetForm: function() {
			this.showResults = false;
			this.ez = null;
			this.ez_img = null;
			this.hsn = null;
			this.hsn_img = null;
			this.field_2_2 = null;
			this.field_2_2_img = null;
			this.vin = null;
			this.vin_img = null;
			this.d3 = null;
			this.d3_img = null;
			this.registrationNumber = null;
			this.registrationNumber_img = null;
			this.name1 = null;
			this.name1_img = null;
			this.name2 = null;
			this.name2_img = null;
			this.firstname = null;
			this.firstname_img = null;
			this.address1 = null;
			this.address1_img = null;
			this.address2 = null;
			this.address2_img = null;
		},
		copy: function(id) {
			const i = document.querySelector('#' + id);
			i.focus();
			i.select();
			try {
				document.execCommand('copy');
			} catch (err) {
				console.log('Unable to copy');
			}
		},
		onAccessKeyChanged: function() {
			localStorage.setItem('accessKey', this.accessKey);
		},
		handleFileChange: function (e) {
			const file = e.target.files[0];
			this.files = e.target.files;
			this.filename = file.name;
			this.url = URL.createObjectURL(file);
		},
		cancel: function () {
			this.filename = 'Kein Dokument ausgewählt.';
			this.url = null;
			this.$refs.selectedFile.value = null;
			this.resetForm();
		},
		scan: function () {
			this.resetForm();
			let fd = new FormData();
			
			if (this.accessKey === '') {
				this.error = true;
				this.errorMsg = 'Access-Key ist ungültig'
				return;
			}

			fd.append('file', this.files[0]);
			fd.append('show_cuts', true);
			fd.append('access_key', this.accessKey);
			this.loading = true;
			this.error = false;
			this.errorMsg = '';
			const that = this;
			const url = "https://api.fahrzeugschein-scanner.de/";

			axios.post(url, fd)
				.then(resp => {
					this.mapResponse(resp.data);
					this.loading = false;
					this.showResults = true;
				})
				.catch(function (error) {
					// handle error
					console.log(error);
					
					that.loading = false;
					that.error = true;
					that.showResults = false;

					if (error.response.status === 401) {
						that.errorMsg = 'Access-Key ist ungültig'
					} else if (error.response.status === 415) {
						that.errorMsg = 'Dateiformat nicht unterstützt'
					} else {
						that.errorMsg = 'Server error'
					}
				});
		},
		mapResponse: function (data) {
			this.ez = data.ez_string;
			this.hsn = data.hsn;
			this.field_2_2 = data.field_2_2;
			this.vin = data.vin;
			this.d3 = data.d3;
			this.registrationNumber = data.registrationNumber;
			this.name1 = data.name1;
			this.name2 = data.name2;
			this.firstname = data.firstname;
			this.address1 = data.address1;
			this.address2 = data.address2;
			
			if (data.ez_img != null && data.ez_img != '') this.ez_img = 'data:image/png;charset=utf-8;base64, ' + data.ez_img;
			if (data.hsn_img != null && data.hsn_img != '') this.hsn_img = 'data:image/png;charset=utf-8;base64, ' + data.hsn_img;
			if (data.field_2_2_img != null && data.field_2_2_img != '') this.field_2_2_img = 'data:image/png;charset=utf-8;base64, ' + data.field_2_2_img;
			if (data.vin_img != null && data.vin_img != '') this.vin_img = 'data:image/png;charset=utf-8;base64, ' + data.vin_img;
			if (data.d3_img != null && data.d3_img != '') this.d3_img = 'data:image/png;charset=utf-8;base64, ' + data.d3_img;
			if (data.registrationNumber_img != null && data.registrationNumber_img != '') this.registrationNumber_img = 'data:image/png;charset=utf-8;base64, ' + data.registrationNumber_img;
			if (data.name1_img != null && data.name1_img != '') this.name1_img = 'data:image/png;charset=utf-8;base64, ' + data.name1_img;
			if (data.name2_img != null && data.name2_img != '') this.name2_img = 'data:image/png;charset=utf-8;base64, ' + data.name2_img;
			if (data.firstname_img != null && data.firstname_img != '') this.firstname_img = 'data:image/png;charset=utf-8;base64, ' + data.firstname_img;
			if (data.address1_img != null && data.address1_img != '') this.address1_img = 'data:image/png;charset=utf-8;base64, ' + data.address1_img;
			if (data.address2_img != null && data.address2_img != '') this.address2_img = 'data:image/png;charset=utf-8;base64, ' + data.address2_img;
		}
	},
	created: function() {
		const key = localStorage.getItem('accessKey');

		if (key !== null) {
			this.accessKey = key;
		}
	}
}
</script>

