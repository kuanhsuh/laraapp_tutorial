<template>
    <div class="container">
        <div class="row mt-5">
            <div class="col-md-12">
                <div class="card">
                    <div class="card-header">
                        <h3 class="card-title">Users Table</h3>
                        <div class="card-tools">
                            <button class="btn btn-success" data-toggle="modal" data-target="#exampleModal">Add New
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
                                <tr v-for="user in users" :key="user.id">
                                    <td>{{user.id}}</td>
                                    <td>{{user.name}}</td>
                                    <td>{{user.email}}</td>
                                    <td>{{user.type | upText}}</td>
                                    <td>{{user.created_at | myDate}}</td>
                                    <!-- <td>
                                        <span class="tag tag-success">Approved</span>
                                    </td> -->
                                    <td>
                                        <a href="#">
                                            <i class="fa fa-edit blue"></i>
                                        </a> |
                                        <a href="#">
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

        <!-- Modal -->
        <div class="modal fade" id="exampleModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="exampleModalLabel">Add New</h5>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                            <span aria-hidden="true">&times;</span>
                        </button>
                    </div>
                    <div class="modal-body">
                        <form @submit.prevent="createUser">
                            <div class="form-group">
                                <input v-model="form.name" type="text" name="name" id="name" class="form-control" :class="{ 'is-invalid': form.errors.has('name') }" placeholder="Name">
                                <has-error :form="form" field="name"></has-error>
                            </div>
                            <div class="form-group">
                                <input v-model="form.email" type="text" id="email" name="email" class="form-control" :class="{ 'is-invalid': form.errors.has('email') }" placeholder="Email">
                                <has-error :form="form" field="email"></has-error>
                            </div>
                            <div class="form-group">
                                <textarea v-model="form.bio" type="text" id="bio" name="bio" class="form-control" :class="{ 'is-invalid': form.errors.has('bio') }" placeholder="Short Bio for user (optional)"></textarea>
                                <has-error :form="form" field="bio"></has-error>
                            </div>

                            <div class="form-group">
                                <select name="type" v-model="form.type" id="type" class="form-control" :class="{ 'is-invalid': form.errors.has('type') }">
                                    <option value="">Select User Role</option>
                                    <option value="admin">Admin</option>
                                    <option value="user">Standard User</option>
                                    <option value="author">Author</option>
                                </select>
                                <has-error :form="form" field="type"></has-error>
                            </div>

                            <div class="form-group">
                                <input v-model="form.password" id="password" type="password" name="password" class="form-control" :class="{ 'is-invalid': form.errors.has('password') }" placeholder="Password">
                                <has-error :form="form" field="password"></has-error>
                            </div>
                            <div class="modal-footer">
                                <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                                <button type="submit" class="btn btn-primary">Create</button>
                            </div>
                        </form>
                    </div>

                </div>
            </div>
        </div>
    </div>

</template>

<script>
import Form from "vform"

export default {
  data() {
    return {
      users: [],
      form: new Form({
        name: "",
        email: "",
        password: "",
        type: "",
        bio: "",
        photo: ""
      })
    }
  },
  methods: {
    createUser() {
      this.$Progress.start()
      this.form.post("/api/user")
      Fire.$emit("AfterCreate")
      $("#exampleModal").modal("hide")
      toast.fire({
        icon: "success",
        title: "User Created Successfully"
      })
      this.$Progress.finish()
    },
    loadUsers() {
      axios.get("api/user").then(({ data }) => (this.users = data.data))
    }
  },
  created() {
    this.loadUsers()
    Fire.$on("AfterCreate", () => {
      this.loadUsers()
    })
    // setInterval(() => this.loadUsers(), 3000)
  }
}
</script>
