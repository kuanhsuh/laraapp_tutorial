<template>
  <div class="container">
    <div
      class="row mt-5"
      v-if="$gate.isAdminOrAuthor()"
    >
      <div class="col-md-12">
        <div class="card">
          <div class="card-header">
            <h3 class="card-title">Users Table</h3>
            <div class="card-tools">
              <button
                class="btn btn-success"
                @click="newModal"
              >Add New
                <i class="fas fa-user-plus"></i>
              </button>
            </div>
          </div>
          <!-- /.card-header -->
          <div class="card-body table-responsive p-0">
            <table class="table table-hover">
              <thead>
                <tr>
                  <th>ID</th>
                  <th>Name</th>
                  <th>Email</th>
                  <th>Type</th>
                  <th>Registered</th>
                  <th>Modify</th>
                </tr>
              </thead>
              <tbody>
                <tr
                  v-for="user in users"
                  :key="user.id"
                >
                  <td>{{user.id}}</td>
                  <td>{{user.name}}</td>
                  <td>{{user.email}}</td>
                  <td>{{user.type | upText}}</td>
                  <td>{{user.created_at | myDate}}</td>
                  <!-- <td>
                                        <span class="tag tag-success">Approved</span>
                                    </td> -->
                  <td>
                    <a
                      href="#"
                      @click="editModal(user)"
                    >
                      <i class="fa fa-edit blue"></i>
                    </a> |
                    <a
                      href="#"
                      @click="deleteUser(user.id)"
                    >
                      <i class="fa fa-trash red"></i>
                    </a>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
          <!-- /.card-body -->
        </div>
        <!-- /.card -->
      </div>
    </div>

    <div v-if="!$gate.isAdminOrAuthor()">
      <not-found></not-found>
    </div>
    <!-- Modal -->
    <div
      class="modal fade"
      id="exampleModal"
      tabindex="-1"
      role="dialog"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div
        class="modal-dialog modal-dialog-centered"
        role="document"
      >
        <div class="modal-content">
          <div class="modal-header">
            <h5
              v-show="!editMode"
              class="modal-title"
              id="exampleModalLabel"
            >Add New</h5>
            <h5
              v-show="editMode"
              class="modal-title"
              id="exampleModalLabel"
            >Update User's Info</h5>
            <button
              type="button"
              class="close"
              data-dismiss="modal"
              aria-label="Close"
            >
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            <form @submit.prevent="editMode ? updateUser() : createUser()">
              <div class="form-group">
                <input
                  v-model="form.name"
                  type="text"
                  name="name"
                  id="name"
                  class="form-control"
                  :class="{ 'is-invalid': form.errors.has('name') }"
                  placeholder="Name"
                >
                <has-error
                  :form="form"
                  field="name"
                ></has-error>
              </div>
              <div class="form-group">
                <input
                  v-model="form.email"
                  type="text"
                  id="email"
                  name="email"
                  class="form-control"
                  :class="{ 'is-invalid': form.errors.has('email') }"
                  placeholder="Email"
                >
                <has-error
                  :form="form"
                  field="email"
                ></has-error>
              </div>
              <div class="form-group">
                <textarea
                  v-model="form.bio"
                  type="text"
                  id="bio"
                  name="bio"
                  class="form-control"
                  :class="{ 'is-invalid': form.errors.has('bio') }"
                  placeholder="Short Bio for user (optional)"
                ></textarea>
                <has-error
                  :form="form"
                  field="bio"
                ></has-error>
              </div>

              <div class="form-group">
                <select
                  name="type"
                  v-model="form.type"
                  id="type"
                  class="form-control"
                  :class="{ 'is-invalid': form.errors.has('type') }"
                >
                  <option value="">Select User Role</option>
                  <option value="admin">Admin</option>
                  <option value="user">Standard User</option>
                  <option value="author">Author</option>
                </select>
                <has-error
                  :form="form"
                  field="type"
                ></has-error>
              </div>

              <div class="form-group">
                <input
                  v-model="form.password"
                  id="password"
                  type="password"
                  name="password"
                  class="form-control"
                  :class="{ 'is-invalid': form.errors.has('password') }"
                  placeholder="Password"
                >
                <has-error
                  :form="form"
                  field="password"
                ></has-error>
              </div>
              <div class="modal-footer">
                <button
                  type="button"
                  class="btn btn-secondary"
                  data-dismiss="modal"
                >Close</button>
                <button
                  v-show="editMode"
                  type="submit"
                  class="btn btn-success"
                >Update</button>
                <button
                  v-show="!editMode"
                  type="submit"
                  class="btn btn-primary"
                >Create</button>
              </div>
            </form>
          </div>

        </div>
      </div>
    </div>
  </div>

</template>

<script>
import Form from "vform";
import NotFound from "./NotFound";

export default {
  data() {
    return {
      editMode: false,
      users: [],
      form: new Form({
        id: "",
        name: "",
        email: "",
        password: "",
        type: "",
        bio: "",
        photo: ""
      })
    };
  },
  components: {
    "not-found": NotFound
  },
  methods: {
    updateUser() {
      this.$Progress.start();

      this.form
        .put("api/user/" + this.form.id)
        .then(() => {
          $("#exampleModal").modal("hide");
          swal.fire("Updated!", "Information has been updated!", "success");
          this.$Progress.finish();
          Fire.$emit("AfterCreate");
        })
        .catch(() => {
          this.$Progress.fail();
        });
    },
    editModal(user) {
      this.editMode = true;
      this.form.reset();
      $("#exampleModal").modal("show");
      this.form.fill(user);
    },
    newModal() {
      this.editMode = false;
      this.form.reset();
      $("#exampleModal").modal("show");
    },
    deleteUser(id) {
      swal
        .fire({
          title: "Are you sure?",
          text: "You won't be able to revert this!",
          icon: "warning",
          showCancelButton: true,
          confirmButtonColor: "#3085d6",
          cancelButtonColor: "#d33",
          confirmButtonText: "Yes, delete it!"
        })
        .then(result => {
          this.form
            .delete("/api/user/" + id)
            .then(() => {
              if (result.value) {
                swal.fire("Deleted!", "Your file has been deleted.", "success");
                Fire.$emit("AfterCreate");
              }
            })
            .catch(() =>
              swal.fire("Failed!", "Something was wrong", "warning")
            );
        });
    },
    createUser() {
      this.$Progress.start();
      this.form.post("/api/user").then(() => {
        Fire.$emit("AfterCreate");
        $("#exampleModal").modal("hide");
        toast.fire({
          icon: "success",
          title: "User Created Successfully"
        });
        this.$Progress.finish();
      });
    },
    loadUsers() {
      console.log(this.$gate);
      if (this.$gate.isAdminOrAuthor()) {
        axios.get("api/user").then(({ data }) => (this.users = data.data));
      }
    }
  },
  created() {
    this.loadUsers();
    Fire.$on("AfterCreate", () => {
      this.loadUsers();
    });
    // setInterval(() => this.loadUsers(), 3000)
  }
};
</script>
