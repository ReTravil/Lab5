<template>
    <Page>
        <ActionBar class="header" title="My todo"/>
        <StackLayout>
            <TextField class="input" v-model="todoText" hint="Write your todo..."  @returnPress="createTodo()"/>
            <ScrollView orientation="horizontal">
                <ListView class="task" for="el in data">
                    <v-template>
                        <GridLayout columns="200%, 70%, 70%">
                            <Label class="task-text done"  v-if="el.done" textWrap="true" col="0">{{el.title}}</Label>
                            <Label class="task-text"  v-else  @tap="updateTodo(el.id, el.title)" textWrap="true" col="0">{{el.title}}</Label>
                            <Button class="btn check-btn" text='ok' @tap="hideTodo(el.id)" col="1"/>
                            <Button class="btn remove-btn" text="X" @tap="deleteTodo(el.id)" col="2"/> 
                        </GridLayout>
                    </v-template>
                </ListView>
            </ScrollView>
        </StackLayout>
    </Page>
</template>

<script>
import * as ApplicationSettings from "application-settings";
export default {
    data: function() {
        return {
            todoText: '',
            data: []
        }
    },

    methods: {
        createTodo(){
            if(this.todoText != ''){
                this.data.push({
                    id: Math.random(),
                    title: this.todoText,
                    done: false
                });
                this.todoText = '';
            }
            this.save();
        },

        hideTodo(id){
            this.data = this.data.map(el => {
                if (el.id == id) 
                    el.done = !el.done;
                return el;
            })
            this.save();
        },

        deleteTodo(id){
            this.data = this.data.filter(el => el.id !== id);
            this.save();
        },

        save(){
            let saveData = Object.assign({}, this.data);
            ApplicationSettings.setString('data', JSON.stringify(saveData));
        },

        updateTodo(id, text) {
            prompt({
                title: "edit",
                message: "Your Todo ",
                okButtonText: "update",
                cancelButtonText: "cancel",
                defaultText: text,
            })
            .then(result => {
                this.data.forEach(el => {
                    if (el.id == id && result.text != ''){
                        el.title = result.text;
                        this.save();
                    }    
                });
            })
        }
    },

    mounted() {
        if(ApplicationSettings.getString('data'))
            this.data = Object.values(JSON.parse(ApplicationSettings.getString('data')));
    }
}
</script>

<style scoped>

    Page {
        background-color: rgb( 32, 34, 36);
    }

    .header{
        background-color: #000;
        color: #fff;
    }

    .app{
        background-color: #373737;
    }

    .btn{
        background-color: #000;
        color: #fff;
        border-radius: 10;
        border-color: #000;
    }

    .task-text{
        margin: 30 20;
        border-radius: 10;
        padding: 10;
        text-align: center;
        background-color: black;
        color: #fff;
    }

    .done {
        text-decoration: line-through;
        background-color: #5f5f5f;
    }

    .input{
        background-color: black;
        color: #fff;
        margin: 50px 30px;
        text-align: center;
    }

    .input:focus{
        background-color: black;
        color: #fff;
    }
</style>