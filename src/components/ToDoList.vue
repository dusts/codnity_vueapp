<style>
    table {
        font-family: arial, sans-serif;
        border-collapse: collapse;
        width: 100%;
    }

    td, th {
        border: 1px solid #dddddd;
        text-align: left;
        padding: 8px;
    }

    tr:nth-child(even) {
        background-color: #dddddd;
    }
</style>

<template>
    <div class="post">
        <div>Basic ToDo list</div>
        <div v-if="post" class="content">
            <table>
                <thead>
                    <tr>
                        <th>Id</th>
                        <th>Name</th>
                        <th>IsDone</th>
                        <th>Delete</th>
                    </tr>
                </thead>

                <tbody>
                    <tr v-for="todolistitem in post" :key="todolistitem.id">
                        <td>{{ todolistitem.id }}</td>
                        <td>{{ todolistitem.name }}</td>
                        <td>{{ todolistitem.isDone }}</td>
                        <td><button @click="deleteById(todolistitem.id)">Delete me!</button></td>
                    </tr>
                    <tr>
                        <td><button @click="addNewItem(fname)">Add new ToDo list item!</button></td>
                        <td><input type="text" class="form-control" v-model="fname" /></td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>

<script lang="js">
    import { defineComponent } from 'vue';
    // import { ref } from 'vue'
    //const item = ref('Vue.js')

    export default defineComponent({
        data() {
            return {
                loading: false,
                post: null
            };
        },
        created() {
            // fetch the data when the view is created and the data is
            // already being observed
            this.fetchData();
        },
        watch: {
            // call again the method if the route changes
            '$route': 'fetchData'
        },
        methods: {
            fetchData() {
                this.post = null;
                this.loading = true;

                fetch('api/todolistitems', {method: 'GET'})
                    .then(r => r.json())
                    .then(json => {
                        this.post = json;
                        this.loading = false;
                        return;
                    });
            },
            deleteById(id) {
                fetch('api/todolistitems/' + id, { method: 'DELETE' }).then(() => window.location.reload()); // for now we just reload on adding
            },
            addNewItem(name) {
                const requestOptions = {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({ name: name })
                };
                //examples taken from https://jasonwatmore.com/post/2020/04/30/vue-fetch-http-post-request-examples
                //fetch("", requestOptions)
                //    .then(response => response.json())
                //    .then(data => (this.postId = data.id));
                fetch('api/todolistitems', requestOptions).then(() => window.location.reload()); // for now we just reload on adding
            }
        },
    });
</script>