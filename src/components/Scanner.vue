<template>
   <div class="container">
		!-- file upload - all-->
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
   </div>
</template>

<script>
export default {
  	name: 'Scanner',
	data: () => ({
		url: null,
		files: null,
		filename: 'Kein Dokument ausgewählt.',
		loading: false,
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

			axios.post('/', fd)
				.then(resp => {
					this.mapResponse(resp.data);
					this.loading = false;
				})
				.catch(function (error) {
					// handle error
					console.log(error);
					
					that.loading = false;
					that.error = true;

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

