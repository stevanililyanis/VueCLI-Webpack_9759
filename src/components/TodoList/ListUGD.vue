<template>
 <v-main class="list">
    <h3 class="text-h3 font-weight-medium mb-5">To Do List</h3>
    <v-card>
    <v-card-title>
            <v-text-field
                v-model="search"
                append-icon="mdi-magnify"
                label="Search"
                single-line
                hide-details
            >
            </v-text-field>
       
        <v-spacer></v-spacer>
        <v-select
        v-model="sortable"
        :items="['Penting', 'Biasa', 'Tidak penting']"
        label="Sort Data"
        style="width: 30%;"
        >
         </v-select>

        <v-btn color="success" dark @click="dialog = true">
        Tambah
        </v-btn>
        </v-card-title>
        <v-data-table :headers="headers" :items="todos" :search="search" :expanded.sync="expanded"
        item-key="note"
        show-expand>
            <template v-slot:[`item.priority`]="{ item }">
                <td>
                    <v-chip v-if="item.priority == 'Penting'" class="ma-2" close color="red"label outlined>
                        {{ item.priority }}
                    </v-chip>
                    <v-chip v-else-if="item.priority == 'Biasa'" class="ma-2" close color="success"label outlined>
                        {{ item.priority }}
                    </v-chip>
                    <v-chip v-else class="ma-2" close color="primary"label outlined>
                        {{ item.priority }}
                    </v-chip>
                </td>
            </template>
           

            <template v-slot:[`item.actions`]="{ item }">
                <v-btn small class="mr-2" @click="editItem(item)">
                    edit
                </v-btn>
                <v-btn small @click="deleteItem(item)">
                delete
                </v-btn>
            </template>

             <template v-slot:[`expanded-item`]="{ item }">
                    <td :colspan="headers.length">
                        {{ item.note }}
                    </td>
                </template>
        </v-data-table>
    </v-card>
    <v-dialog v-model="dialog" persistent max-width="600px">
    <v-card>
        <v-card-title>
        <span class="headline">Form Todo</span>
        </v-card-title>
            <v-card-text>
            <v-container>
                <v-text-field
                    v-model="formTodo.task"
                    label="Task"
                    required
                    >
                </v-text-field>
                <v-select
                    v-model="formTodo.priority"
                    :items="['Penting', 'Biasa', 'Tidak penting']"
                    label="Priority"
                    required
                    >
                </v-select>
                <v-textarea
                    v-model="formTodo.note"
                    label="Note"
                    required
                ></v-textarea>
            </v-container>
        </v-card-text>
    <v-card-actions>
        <v-spacer></v-spacer>
            <v-btn color="blue darken-1" text @click="cancel">
                Cancel
            </v-btn>
            <v-btn color="blue darken-1" text @click="save">
                Save
            </v-btn>
        </v-card-actions>
    </v-card>
    </v-dialog>

    <v-dialog v-model="edit" persistent max-width="400px">
        <v-card>
            <v-card-title>
                <span class="headline">
                    Edit To do
                </span>
            </v-card-title>
            
            <v-card-text>
                <v-container>
                    <v-text-field
                    v-model="formTodo.task"
                    label="Task" v-bind:placeholder="formTodo.task"
                    
                    >
                    
                </v-text-field>
                <v-select
                    v-model="formTodo.priority"
                    :items="['Penting', 'Biasa', 'Tidak penting']"
                    label="Priority"
                    v-bind:selected="formTodo.priority"
                    >
                </v-select>
                <v-textarea
                    v-model="formTodo.note"
                    label="Note"
                    v-bind:placeholder="formTodo.note"
                    required
                ></v-textarea>
                    
                </v-container>
            </v-card-text>

            <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="red darken-1" text @click="cancel">
                    Cancel
                </v-btn>
            </v-card-actions>
            <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="green darken-1" text @click="saveEdit(formTodo)">
                    Save
                </v-btn>
            </v-card-actions>

        </v-card>
    </v-dialog>

    <v-dialog v-model="dialogDelete" persistent max-width="400px">
        <v-card>
            <v-card-title>
                <span class="headline3">
                    Yakin ingin menghapus?
                </span>
            </v-card-title>

            <v-card-actions>
                <v-spacer></v-spacer>
                
                <v-btn color="green darken-1" text @click="cancel">
                    Tidak
                </v-btn>

                <v-btn color="red darken-1" text @click="confirmDelete">
                    Ya
                </v-btn>

            </v-card-actions>

        </v-card>
    </v-dialog>

 </v-main>
</template>
<script>
export default {
    name: "List",
    data() {
        return {
        search: null,
        dialog: false,
        edit:false,
        tempItem:null,
        dialogDelete:false,
        singleExpand: true,
        headers: [
                {
                    text: "Task",
                    align: "start",
                    sortable: true,
                    value: "task",
                },
                { text: "Priority", value: "priority" },
                { text: "Note", value: "note" },
                { text: "Actions", value: "actions" },
            ],
        todos: [
                {
                task: "bernafas",
                priority: "Penting",
                note: "huffttt",
                },
                {
                task: "nongkrong",
                priority: "Tidak penting",
                note: "bersama tman2",
                },
                {
                task: "masak",
                priority: "Biasa",
                note: "masak air 500ml",
                },
            ],
            detail: {
                task: null,
                priority: null,
                note: null,
            },
            formTodo: {
                task: null,
                priority: null,
                note: null,
            },
        };
    },
    methods: {
        save() {
            this.todos.push(this.formTodo);
            this.resetForm();
            this.dialog = false;
        },
        cancel() {
            this.resetForm();
            this.dialog = false;
            this.dialogDelete=false;
            this.edit=false;
        },
        resetForm() {
        this.formTodo = {
            task: null,
            priority: null,
            note: null,
            };
        },
        editItem(item){
            this.formTodo= {
                task: item.task,
                priority: item.priority,
                note: item.note,
            },
            this.edit=true
            this.tempItem=item;
        },
        saveEdit(formTodo) {
            this.tempItem.task = formTodo.task;
            this.tempItem.priority = formTodo.priority;
            this.tempItem.note = formTodo.note;
            this.edit=false;
        },
        deleteItem(item){
            this.tempItem=item;
            this.dialogDelete=true; 
        },
        confirmDelete(){
            this.todos.splice(this.tempItem, 1)
            this.dialogDelete=false;
        }
    },
};
</script>
