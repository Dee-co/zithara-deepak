<template>
  <div class="popup">
  <div class="text-end ">
    <v-btn class="ma-2"  prepend-icon="mdi-plus"
 color="primary" @click="add_btn"> New </v-btn>
  </div>
  <v-dialog v-model="dialog" width="400">
    <v-card>
      <div class="d-flex justify-space-between bg-primary">
        <v-card-title class="headline" >Detail</v-card-title>
          <v-btn icon @click="dialog = false" size="small" variant="plain">
        <v-icon >mdi-close</v-icon>
      </v-btn>
      </div>
      
      <v-form ref="form" @submit.prevent class="ma-4">
        <v-text-field
          v-model="name"
          require
          :rules="nameRules"
          label="Name"
          variant="outlined"
        ></v-text-field>
        <v-text-field
          v-model="email"
          required
          :rules="emailRules"
          label="Email"
          variant="outlined"
        ></v-text-field>
        <v-text-field
          v-model="phone"
         required
          :rules="phoneRules"
          label="Phone"
          variant="outlined"
        ></v-text-field>
        <v-btn
          type="submit"
          block
          class="mt-2 round-sm"
          @click="submit_data"
          
          v-if="add_data"
          :disabled="hasErrors"
          >Submit</v-btn
        >
        <v-btn
          type="submit"
          block
          class="mt-2 round-sm"
          @click="updateData"
          v-if="update_data"
          :disabled="hasErrors"
          >Update</v-btn
        >
      </v-form>
    </v-card>
  </v-dialog>
  <p v-if="submitted_data.length < 1" class="no_record_style">No Record Found</p>
  <v-table
    class="text-center ma-2 table_style "
    v-if="submitted_data.length > 0"
  >
    <thead>
      <tr>
        <th class="text-center text-primary">Name</th>
        <th class="text-center text-primary">Email</th>
        <th class="text-center text-primary">Phone</th>
        <th class="text-center text-primary">Actions</th>
      </tr>
    </thead>
    <tbody class="align-center">
      <tr v-for="item in submitted_data" :key="item.name" class="align-center" style="margin: 10px;">
        <td>{{ item.name }}</td>
        <td>{{ item.email }}</td>
        <td>{{ item.phone }}</td>
        <td>
          <v-btn size="small" @click="editData(item)" color="primary" prepend-icon="mdi-pen"
            >Edit</v-btn
          >
          &nbsp;
          <v-btn
            @click="deleteData(item.id)"
            size="small" color="red"
            prepend-icon="mdi-delete"
            >Delete</v-btn
          >
        </td>
      </tr>
    </tbody>
  </v-table>
  <v-dialog v-model="showDeleteAlert" width="400">
      <v-card>
        <v-card-title >Delete confirmation</v-card-title>
        <v-card-text>
          Are you sure you want to delete this item?
        </v-card-text>
        <v-card-actions class="d-flex justify-end">
          <v-btn color="primary" text @click="showDeleteAlert = false">
            Cancel
          </v-btn>
          <v-btn color="error" @click="sureDeleteData()" text>
            Delete
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
</div>
</template>
<script>
export default {
  data() {
    return {
      dialog: false,
      name: "",
      email: "",
      phone: "",
      submitted_data: [],
      editIndex: -1,
      update_data: false,
      showDeleteAlert:false,
      selected_delete_id:"",
      add_data: true,
      nameRules: [(v) => !!v || "Name is required"],
      emailRules: [
        (v) => !!v || "Email is required",
        (v) => /.+@.+\..+/.test(v) || "Email must be valid",
      ],
      phoneRules: [
        (v) => !!v || "Phone is required",
        (v) => /^\d{10}$/.test(v) || "Phone must be a 10-digit number",
      ],
    };
  },
  computed: {
    nameErrors() {
      const errors = [];
      if (!this.name) errors.push("Name is required");
      return errors;
    },
    emailErrors() {
      const errors = [];
      if (!this.email) errors.push("Email is required");
      if (!/.+@.+\..+/.test(this.email)) errors.push("Email must be valid");
      return errors;
    },
    phoneErrors() {
      const errors = [];
      if (!this.phone) errors.push("Phone is required");
      if (!/^\d{10}$/.test(this.phone))
        errors.push("Phone must be a 10-digit number");
      return errors;
    },
    hasErrors() {
      // Check if any of the error arrays have content (i.e., validation errors exist)
      return (
        this.nameErrors.length ||
        this.emailErrors.length ||
        this.phoneErrors.length
      );
    },
  },
  methods: {
    generateUniqueId() {
      const timestamp = new Date().getTime().toString(36);
      const randomStr = Math.random().toString(36).substr(2, 5);
      return timestamp + randomStr;
    },
    add_btn() {
      (this.dialog = true), (this.update_data = false);
      this.add_data = true;
      this.name = "";
      this.email = "";
      this.phone = "";
      this.editIndex = -1;
    },
    submit_data() {
      const new_data = {
        id: this.generateUniqueId(),
        name: this.name,
        email: this.email,
        phone: this.phone,
      };
      this.submitted_data.push(new_data);
      this.dialog = false;
      this.name = "";
      this.email = "";
      this.phone = "";
    },
    deleteData(index) {
      this.showDeleteAlert = true;
      this.selected_delete_id = index
    },
    sureDeleteData(){
      this.submitted_data.splice(this.selected_delete_id, 1);
      this.showDeleteAlert = false;
    },
    editData(data) {
      this.dialog = true;
      this.update_data = true;
      this.add_data = false;
      this.name = data.name;
      this.email = data.email;
      this.phone = data.phone;
      this.editIndex = this.submitted_data.indexOf(data);
    },
    updateData() {
      // Check if the editIndex is valid
      if (this.editIndex !== -1) {
        // Update the data at the editIndex with the new values
        this.submitted_data[this.editIndex].name = this.name;
        this.submitted_data[this.editIndex].email = this.email;
        this.submitted_data[this.editIndex].phone = this.phone;

        // Reset form fields and flags after the update
        this.dialog = false;
        this.name = "";
        this.email = "";
        this.phone = "";
        this.editIndex = -1;
        this.update_data = false;
      }
    },
  },
};
</script>
<style>
  .table_style{
    border: 1px solid rgb(240, 202, 202);
  }
  tr th{
    color: blueviolet;
  }
  .no_record_style {
  font-size: 18px;
  color: #777;
  margin: 20px; 
  text-align: center;
}
</style>