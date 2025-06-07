<template>
 <div >
  <div class="custom-outer-form"> 
  <div >
      <form @submit.prevent="handleSubmit">
        <div v-for="(group, index) in groups" :key="index" class="border p-3 mb-3">
          <div class="row mb-2">
            <div class="col-md-4">
              <label>Name</label>
              <input v-model="group.name" type="text" class="form-control" required />
            </div>
            <div class="col-md-4">
              <label>Email</label>
              <input v-model="group.email" type="email" class="form-control" required />
            </div>
            <div class="col-md-4">
              <label>Mobile</label>
              <input v-model="group.mobile" type="text" class="form-control" placeholder="(844) 448-0110" required />
            </div>
          </div>
          <button v-if="groups.length > 2" type="button" class="btn btn-danger btn-sm" @click="removeGroup(index)">Remove</button>
        </div>
        <button type="button" class="btn btn-primary mb-3" @click="addGroup">+ Add More</button>

        <div class="row mb-3">
          <div class="col-md-4">
            <label>Birth Date</label>
            <input v-model="form.birthDate" type="date" class="form-control" required />
          </div>
         </div> 

         <div class="col-md-4">
          <label class="form-label">Gender</label>
          <div class="row">
            <div class="col-6">
              <div class="form-check d-flex align-items-center gap-2">
                <input class="form-check-input" type="radio" id="genderMale" value="Male" v-model="form.gender" />
                <label class="form-check-label mb-0" for="genderMale">Male</label>
              </div>
            </div>
            <div class="col-6">
              <div class="form-check d-flex align-items-center gap-2">
                <input class="form-check-input" type="radio" id="genderFemale" value="Female" v-model="form.gender" />
                <label class="form-check-label mb-0" for="genderFemale">Female</label>
              </div>
            </div>
          </div>
        </div>

          <div class="col-md-4">
            <label>Languages</label>
            <div class="form-check form-check-inline" v-for="lang in languages" :key="lang">
              <input class="form-check-input" type="checkbox" :value="lang" v-model="form.languages" />
              <label class="form-check-label">{{ lang }}</label>
            </div>
          </div>
       

        <div class="row mb-3">
          <div class="col-md-6">
            <label>City</label>
            <select v-model="form.cities" class="form-select" multiple required>
              <option v-for="city in cityList" :key="city" :value="city">{{ city }}</option>
            </select>
          </div>

          <div class="col-md-6">
            <label>File Upload (JPEG, PDF)</label>
            <input type="file" @change="handleFileUpload" class="form-control" accept=".jpeg,.jpg,.pdf" />
          </div>
        </div>

        <div v-if="error" class="alert alert-danger">{{ error }}</div>

        <button type="submit" class="btn btn-success">Submit</button>
      </form>

      <div class="mt-5" v-if="submitted">
        <h4>Submitted Data</h4>
        <table class="table table-bordered">
          <thead>
            <tr>
              <th>#</th>
              <th>Name</th>
              <th>Email</th>
              <th>Mobile</th>
              <th>Birth Date</th>
              <th>Gender</th>
              <th>Languages</th>
              <th>Cities</th>
              <th>File</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(group, index) in groups" :key="index">
              <td>{{ index + 1 }}</td>
              <td>{{ group.name }}</td>
              <td>{{ group.email }}</td>
              <td>{{ group.mobile }}</td>
              <td>{{ formattedDate }}</td>
              <td>{{ form.gender }}</td>
              <td>{{ form.languages.join(', ') }}</td>
              <td>{{ form.cities.join(', ') }}</td>
              <td>{{ uploadedFileName }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    </div> 
  </div>

</template>

<script>
export default {
  data() {
    return {
      groups: [
        { name: '', email: '', mobile: '' },
        { name: '', email: '', mobile: '' },
      ],
      form: {
        birthDate: '',
        gender: '',
        languages: [],
        cities: [],
      },
      languages: ['Angular', 'Vuejs', 'ReactJs'],
      cityList: ['New York', 'Los Angeles', 'Chicago', 'Houston', 'Phoenix', 'Dallas', 'Miami', 'Seattle', 'Denver', 'Atlanta'],
      uploadedFile: null,
      uploadedFileName: '',
      error: '',
      submitted: false
    };
  },
  computed: {
    formattedDate() {
      if (!this.form.birthDate) return '';
      return new Date(this.form.birthDate).toLocaleDateString('en-US', {
        year: 'numeric', month: 'long', day: 'numeric'
      });
    }
  },
  methods: {

    addGroup() {
      this.groups.push({ name: '', email: '', mobile: '' });
    },

    removeGroup(index) {
      if (this.groups.length > 2) this.groups.splice(index, 1);
    },

    handleFileUpload(e) {
      const file = e.target.files[0];
      if (file && (file.type === 'application/pdf' || file.type.includes('jpeg') || file.type.includes('jpg'))) {
        this.uploadedFile = file;
        this.uploadedFileName = file.name;
        this.error = '';
      } else {
        this.error = 'Only JPEG and PDF files are allowed.';
        this.uploadedFile = null;
        this.uploadedFileName = '';
      }
    },

    async handleSubmit() {
      console.log('Submitting form...');

      this.error = '';
      this.submitted = false;

      const formData = new FormData();
      formData.append('groups', JSON.stringify(this.groups));
      formData.append('birthDate', this.form.birthDate);
      formData.append('gender', this.form.gender);
      formData.append('languages', JSON.stringify(this.form.languages));
      formData.append('cities', JSON.stringify(this.form.cities));
      if (this.uploadedFile) {
        formData.append('file', this.uploadedFile);
      }

      // Log FormData content for debugging
      for (const pair of formData.entries()) {
        console.log(pair[0], pair[1]);
      }

      try {
        // Use a fake delay for testing submission without backend
        // Remove this and uncomment the fetch to test real API
        await new Promise(resolve => setTimeout(resolve, 1000));
        this.submitted = true;
        console.log('Form submitted successfully!');
        
        /*
        // Uncomment this when backend is ready
        const token = 'YOUR_JWT_TOKEN'; // replace with real token or remove header
        const response = await fetch('/api/submit-form', {
          method: 'POST',
          headers: {
            Authorization: `Bearer ${token}`
          },
          body: formData
        });

        if (!response.ok) {
          const err = await response.json();
          throw new Error(err.message || 'Something went wrong');
        }

        this.submitted = true;
        console.log('Form submitted successfully!');
        */

      } catch (err) {
        this.error = err.message;
        console.error('Error submitting form:', err);
      }
    }

  }
};
</script>

<style scoped>
.custom-outer-form {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  padding: 30px 15px;
  background: linear-gradient(135deg, #f0f9ff, #cbf3f0);
}

form {
  width: 100%;
  max-width: 900px;
  background: #ffffff;
  border-radius: 15px;
  padding: 40px;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.1);
}

/* Form section group styling */
.border.p-3 {
  background-color: #f7fafd;
  border-radius: 10px;
  padding: 20px;
  border: 1px solid #e0e0e0;
}

/* Labels and inputs */
label {
  font-weight: 600;
  display: block;
  margin-bottom: 8px;
  color: #333;
}

input[type="text"],
input[type="email"],
input[type="date"],
input[type="file"],
select,
.form-select {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccddee;
  border-radius: 6px;
  transition: border-color 0.3s;
  margin-bottom: 16px;
}

input[type="text"]:focus,
input[type="email"]:focus,
input[type="date"]:focus,
select:focus {
  border-color: #4aa3df;
  outline: none;
}

/* Buttons */
button {
  margin-top: 8px;
  margin-right: 10px;
}

.btn-primary {
  background-color: #3498db;
  border: none;
}

.btn-danger {
  background-color: #e74c3c;
  border: none;
}

.btn-success {
  background-color: #2ecc71;
  border: none;
}

.btn:hover {
  opacity: 0.9;
}

/* Radios and Checkboxes */
.form-check-input {
  margin-top: 0.3rem;
  margin-right: 8px;
}

.form-check-label {
  margin-right: 20px;
}

/* Error alert */
.alert-danger {
  margin-top: 15px;
  font-size: 14px;
}

/* Table styling for submitted data */
.table {
  margin-top: 30px;
}

.table th, .table td {
  text-align: center;
  vertical-align: middle;
}

h4 {
  margin-top: 40px;
  font-weight: bold;
  color: #2c3e50;
}


.table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 30px;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  box-shadow: 0 4px 12px rgba(0,0,0,0.1);
  border-radius: 8px;
  overflow: hidden;
  background-color: #fff;
}

/* Table header styles */
.table thead {
  background-color: #3498db; /* blue header background */
  color: #fff;
  text-align: center;
  font-weight: 600;
  font-size: 16px;
}

/* Table body */
.table tbody tr {
  border-bottom: 1px solid #ddd;
  transition: background-color 0.3s ease;
}

/* Zebra striping */
.table tbody tr:nth-child(even) {
  background-color: #f9fbfd;
}

/* Hover effect */
.table tbody tr:hover {
  background-color: #e6f2ff;
}

/* Table cells */
.table th,
.table td {
  padding: 12px 15px;
  text-align: center;
  vertical-align: middle;
  border-right: 1px solid #ddd;
}

/* Remove last border-right in each row */
.table th:last-child,
.table td:last-child {
  border-right: none;
}


</style>
