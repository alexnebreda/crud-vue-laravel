<template>
    <div id="app">
    <v-app id="inspire">
        <div>
        <v-toolbar flat color="white">
            <v-toolbar-title>Users</v-toolbar-title>
            <v-divider
            class="mx-2"
            inset
            vertical
            ></v-divider>
            <v-spacer></v-spacer>
            <v-dialog v-model="dialog" max-width="500px">
            <v-btn slot="activator" color="primary" dark class="mb-2">New Item</v-btn>
            <v-card>
                <v-card-title>
                <span class="headline">{{ formTitle }}</span>
                </v-card-title>
                <v-card-text>
                <v-container grid-list-md>
                    <v-layout wrap>
                        <v-flex xs12 sm12 md12>
                            <v-text-field v-model="editedItem.name" label="Nombre"></v-text-field>
                        </v-flex>
                        <v-flex xs12 sm12 md12>
                            <v-text-field v-model="editedItem.lastname" label="Lastname"></v-text-field>
                        </v-flex>
                        <v-flex xs12 sm12 md12>
                            <v-text-field v-model="editedItem.email" label="Email"></v-text-field>
                        </v-flex>
                        <v-flex xs12 sm12 md12>
                            <v-text-field v-model="editedItem.phone" label="Phone"></v-text-field>
                        </v-flex>
                    </v-layout>
                </v-container>
                </v-card-text>
    
                <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" flat @click.native="close">Cancel</v-btn>
                <v-btn color="blue darken-1" flat @click.native="save">Save</v-btn>
                </v-card-actions>
            </v-card>
            </v-dialog>
        </v-toolbar>
        <v-text-field
            v-model="search"
            append-icon="mdi-magnify"
            label="Filter"
            outlined
            hide-details
        ></v-text-field>
        <v-data-table
            :search="search"
            :headers="headers"
            :items="users"
            hide-actions
            class="elevation-1"
        >
            <template slot="items" slot-scope="props">
            <td>{{ props.item.name }}</td>
            <td class="text-xs-right">{{ props.item.name }}</td>
            <td class="text-xs-right">{{ props.item.lastname }}</td>
            <td class="text-xs-right">{{ props.item.email }}</td>
            <td class="text-xs-right">{{ props.item.phone }}</td>
            <td class="justify-center layout px-0">
                <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn
                    color="blue darken-1"
                    text
                    @click="close"
                >
                    Cancel
                </v-btn>
                <v-btn
                    color="blue darken-1"
                    text
                    @click="save"
                >
                    Save
                </v-btn>
                </v-card-actions>
            </td>
            </template>
            <template v-slot:item.actions="{ item }">
                <v-icon
                    small
                    class="mr-2"
                    @click="editItem(item)"
                >
                    mdi-pencil
                </v-icon>
                <v-icon
                    small
                    @click="deleteItem(item)"
                >
                    mdi-delete
                </v-icon>
                </template>
                <template v-slot:no-data>
                <v-btn
                    color="primary"
                    @click="initialize"
                >
                    Reset
                </v-btn>
            </template>
            <template slot="no-data">
            <v-btn color="primary" @click="getApi">Reset</v-btn>
            </template>
        </v-data-table>
        </div>
    </v-app>
    </div>
</template>
<script>
    const token = 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImp0aSI6IjFmMTQ2ZWY3NmU3NzgwZjE5YTkwNjk4ZmU5NDZhN2M5NjdmMjdiMjE4YWNjNTFjNWQ5ODFlNjMyNTBmNTRjNzdlZTY1NzhmZjNlMjMwOWIwIn0.eyJhdWQiOiIyIiwianRpIjoiMWYxNDZlZjc2ZTc3ODBmMTlhOTA2OThmZTk0NmE3Yzk2N2YyN2IyMThhY2M1MWM1ZDk4MWU2MzI1MGY1NGM3N2VlNjU3OGZmM2UyMzA5YjAiLCJpYXQiOjE2MTAzNjY4MzAsIm5iZiI6MTYxMDM2NjgzMCwiZXhwIjoxNjExNjYyODMwLCJzdWIiOiI0NjIiLCJzY29wZXMiOlsiKiJdfQ.RtXFMCBUCcOWFacaOE4IlAmCo3rK-BoPAUJGP4HHK4nfHI4Lef0HIIZQ4faYaokTp1exEi0Qiuf8sit9FDXQVopxkmSVtO70ED2ktQtXPXDTmB03RdsHLJi_-4KdO3pnDydUURdcf2vTRkSeMO85IpbHOV2ozI88CdqUvOwwPP77ITXJBIERC46DyLUrWNm91O9PIUD2xSnIf2BxUxndkTqHMKIKeTwt8yK2dKOnOj5eCgghy-AeJHMc9yYX8yztkPxllcempluuf1u0RaAwtZf99DtFEYH-NizJS-Xu5003aOk2DUJKU3YkMFDtfdj8dTT44S7do1vr1quLrcEBZNmuvJFWQPzEsx4n8TqbwrHTWZjhseBeInBRyX6Z6bh90aIbP1NA2TeSAdkryXBQsZV6yuoKoK_SrYbRAfTlWShj7qBfj4jIhVLl9FnCcrQZat9gGO1y2PUP5HTc7oJAWraQX-U1rmuQ20ovrmy7nDx9Qj4DO1ECfXqXpjzXHSbuJeHhuCtJnskwGNFuQOG7DG3GakeZ4-b1Yummj5nOm3suGjtbrkaVTjMYbWcNko29XnOsMdXtJN5uUPgGc05TD8fXHD9UpsDJJjX0wcl4pFalRneN9h7Nc0Z4kICPertLzNa9kfUOc2iNGBD_opzsKyPLfO-YCFuAl4JgyKErh84';
    export default {
        data: () => ({
            dialog: false,
            dialogDelete: false,
            search: '',
            headers: [
                { text: 'ID', align: 'center', sortable: false, value: 'id'},
                { text: 'Nombre', align: 'center',value: 'attributes.first_name' },
                { text: 'Email', align: 'center', value: 'attributes.email' },
                { text: 'Phone', align: 'center', value: 'attributes.phone_number' },
                { text: 'Creation Date', align: 'center', value: 'creationDate' },
                { text: 'Actions', value: 'actions', sortable: false }
            ],
            users: [],
            editedIndex: -1,
            editedItem: {
                name: '',
                lastname: '',
                email: '',
                phone: 0
            },
            defaultItem: {
                name: '',
                lastname: '',
                email: '',
                phone: 0
            },
        }),
        computed: {
            formTitle () {
                return this.editedIndex === -1 ? 'New User' : 'Edit User'
            },
        },
        watch: {
            dialog (val) {
                val || this.close()
            },
            dialogDelete (val) {
                val || this.closeDelete()
            },
        },
        methods:{
            getApi(){
                axios.get('https://api.qa5.busup.org/beta/users', { 
                headers: {
                    'Content-type': 'application/json',
                    Authorization: `Bearer ${token}` 
                },
                })
                .then((response) => {
                    this.users = response.data.data;
                    console.log(this.users)
                })
                .catch((error) => {
                    console.error(error)
                })
            },
            editItem (item) {
                this.editedIndex = this.users.indexOf(item)
                this.editedItem = Object.assign({}, item)
                this.dialog = true
            },

            deleteItem (item) {
                const index = this.users.indexOf(item)
                confirm('Are you sure you want to delete this item?') && this.users.splice(index, 1)
            },

            close () {
                this.dialog = false
                setTimeout(() => {
                    this.editedItem = Object.assign({}, this.defaultItem)
                    this.editedIndex = -1
                }, 300)
            },

            save () {
                if (this.editedIndex > -1) {
                    Object.assign(this.users[this.editedIndex], this.editedItem)
                } else {
                    this.users.push(this.editedItem)
                }
                this.close()
            }
        },
        created(){
            this.getApi()
        }
    }
</script>

<style scoped>
    .v-input{
        max-width: 25%;
    }
    .v-text-field.v-text-field--enclosed {
        margin: 40px 20px 20px 20px;
    }
    thead{
        background: gray;
    }
</style>