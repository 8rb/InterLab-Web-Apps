<template>
    <v-container class="profileContent">
        <v-card-title class="justify-start">Editar Perfil</v-card-title>
        <v-card v-if="showPersonal" class="profile_card">
            <p>Información Personal</p>
            <v-row>
                <v-col  cols="12" sm="6">
                    <v-text-field
                            label="Nombre"
                            v-model="firstName"
                            :rules="[v => !!v || 'Name is required']"
                            required>
                    </v-text-field>
                </v-col>
                <v-col cols="12" sm="6">
                    <v-text-field
                            label="Apellido" hint="use special characters such as: #$%!"
                            v-model="lastName"
                            :rules="[v => !!v || 'Last Name is required']"
                            required>
                    </v-text-field>
                </v-col>
            </v-row>
            <v-row>
                <v-col cols="12" sm="6">
                    <v-autocomplete
                            ref="country"
                            v-model="country"
                            :rules="[() => !!country || 'This field is required']"
                            :items="countries"
                            label="Pais"
                            placeholder="Select..."
                            required
                    ></v-autocomplete>
                </v-col>
                <v-col cols="12" sm="6">
                    <v-text-field
                            label="Ciudad" hint="use special characters such as: #$%!"
                            v-model="city"
                            :rules="[v => !!v || 'Last Name is required']"
                            required>
                    </v-text-field>
                </v-col>
            </v-row>
            <v-row>
                <v-col cols="12" sm="3">
                    <v-text-field
                            label="Numero de telefono" hint="use special characters such as: #$%!"
                            v-model="phone"
                            :rules="[v => !!v || 'Last Name is required']"
                            required>
                    </v-text-field>
                </v-col>
            </v-row>
        </v-card>
        <v-card v-if="showAcademic" class="profile_card">
            <p>Información Laboral</p>
            <v-row>
                <v-col cols="12" sm="12">
                    <v-data-table
                            :headers="headers"
                            :items="desserts"
                            sort-by="calories"
                            class="elevation-1"
                            cols="22" sm="15"
                    >
                        <template v-slot:top>
                            <v-toolbar flat color="white">
                                <v-toolbar-title>Mis Contactos</v-toolbar-title>
                                <v-divider
                                        class="mx-4"
                                        inset
                                        vertical
                                ></v-divider>
                                <v-spacer></v-spacer>
                                <v-dialog v-model="dialog" max-width="500px">
                                    <template v-slot:activator="{ on, attrs }">
                                        <v-btn
                                                color="primary"
                                                dark
                                                class="mb-2"
                                                v-bind="attrs"
                                                v-on="on"
                                        >New Item</v-btn>
                                    </template>
                                    <v-card>
                                        <v-card-title>
                                            <span class="headline">{{ formTitle }}</span>
                                        </v-card-title>

                                        <v-card-text>
                                            <v-container>
                                                <v-row>
                                                    <v-col cols="12" sm="6" md="4">
                                                        <v-text-field v-model="editedItem.company" label="Dessert company"></v-text-field>
                                                    </v-col>
                                                    <v-col cols="12" sm="6" md="4">
                                                        <v-text-field v-model="editedItem.mailcompany" label="Mail Company"></v-text-field>
                                                    </v-col>
                                                    <v-col cols="12" sm="6" md="4">
                                                        <v-text-field v-model="editedItem.websitecompany" label="WebsiteCompany"></v-text-field>
                                                    </v-col>
                                                    <v-col cols="12" sm="6" md="4">
                                                        <v-text-field v-model="editedItem.number" label="Number Company"></v-text-field>
                                                    </v-col>
                                                </v-row>
                                            </v-container>
                                        </v-card-text>

                                        <v-card-actions>
                                            <v-spacer></v-spacer>
                                            <v-btn color="blue darken-1" text @click="close">Cancel</v-btn>
                                            <v-btn color="blue darken-1" text @click="save">Save</v-btn>
                                        </v-card-actions>
                                    </v-card>
                                </v-dialog>
                            </v-toolbar>
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
                            <v-btn color="primary" @click="initialize">Reset</v-btn>
                        </template>
                    </v-data-table>
                </v-col>
            </v-row>
        </v-card>
        <v-card-actions>
            <v-btn v-if="showAcademic" @click="back()">Regresar</v-btn>
            <v-col class="text-right">
                <v-btn :class="saveButton" @click="saveClick" bottom center class="white--text" color="#4b7bff">Guardar Cambios</v-btn>
            </v-col>
        </v-card-actions>
    </v-container>
</template>

<script>
    export default {
        name: "ProfileCompany",
        props: {
            articles: Array
        },
        data: () => ({
                email: null,
                password: null,
                firstName: null,
                lastName: null,
                isValid: true,
                saveButton: "saveButton",
                country: null,
                showPersonal: true,
                showAcademic: false,
                dialog: false,
                headers: [
                    { text: 'Company', value: 'company' },
                    { text: 'Mail Company', value: 'mailcompany' },
                    { text: 'WebsiteCompany', value: 'websitecompany' },
                    { text: 'Number Company', value: 'number' },
                    { text: 'Actions', value: 'actions', sortable: false },
                ],
                desserts: [],
                editedIndex: -1,
                editedItem: {
                    company: '',
                    mailcompany: '',
                    websitecompany: '',
                    number: '',
                },
                defaultItem: {
                    company: '',
                    mailcompany: '',
                    websitecompany: '',
                    number: '',
                },
            }
        ),
        computed: {
            formTitle () {
                return this.editedIndex === -1 ? 'New Item' : 'Edit Item'
            },
        },

        watch: {
            dialog (val) {
                val || this.close()
            },
        },

        created () {
            this.initialize()
        },

        methods:{
            saveClick(){
                this.showPersonal= !this.showPersonal;
                this.showAcademic= !this.showAcademic;
                this.university = "hello";
                console.log(this.university);
            },
            initialize () {
                this.desserts = []
            },

            editItem (item) {
                this.editedIndex = this.desserts.indexOf(item)
                this.editedItem = Object.assign({}, item)
                this.dialog = true
            },

            deleteItem (item) {
                const index = this.desserts.indexOf(item)
                confirm('Are you sure you want to delete this item?') && this.desserts.splice(index, 1)
            },

            close () {
                this.showPersonal = true;
                this.showAcademic = false;
            },
            back(){
                this.showPersonal = true;
                this.showAcademic = false;
            },

            save () {
                if (this.editedIndex > -1) {
                    Object.assign(this.desserts[this.editedIndex], this.editedItem)
                } else {
                    this.desserts.push(this.editedItem)
                }
                this.close()
            },
        },
    }
</script>

<style scoped>
    .profileContent{
        font-family: 'Khula', sans-serif;
    }

    p{
        color: black;
    }
    .profile_card{
        padding-left: 1vw;
        padding-top: 1vw;
    }
    .saveButton{
        background-color: #4b7bff;

    }
    .v-btn{
        text-transform:none !important;
    }
    .v-text-field{

    }
    .v-card {
        box-shadow: none;
    }

    @media only screen and (max-width: 600px) {
        .Names{
            font-size: 3.5vw;
        }
        .countries{
            font-size: 3.5vw;
        }
        .profile_card{
            padding-left: 2vw;
            padding-top: 2vw;
        }
    }
</style>
