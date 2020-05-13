<template>
  <div id="app">
    <div class="container">
        <div class="row">
            <h1>{{title}}</h1>
        </div>
        <div class="row">
            <div class="form-group w-100">
                <label for="name">Имя</label>
                <input type="text" id="name" class="form-control" v-model="name">
            </div>
            <div class="form-group w-100">
                <label for="surname">Фамилия</label>
                <input type="text" id="surname" class="form-control" v-model="surname">
            </div>
            <div class="form-group w-100">
                <label for="phone">Телефон</label>
                <input type="text" id="phone" class="form-control" v-model="phone">
            </div>
            <div class="btn btn-success" @click="onSave">Сохранить</div>
            <div class="btn btn-primary" @click="onLoad">Получить</div>
        </div>
        <div class="row mt-1" v-for="note in notes" :key="note.id">
            <div class="col-2" v-if="!note.isUnderEditting">{{note.name}}</div>
            <div class="col-2" v-if="note.isUnderEditting"><input type="text" id="name" class="form-control" v-model="nameToEdit"></div>
            <div class="col-2" v-if="!note.isUnderEditting">{{note.surname}}</div>
            <div class="col-2" v-if="note.isUnderEditting"><input type="text" id="surname" class="form-control" v-model="surnameToEdit"></div>
            <div class="col-2" v-if="!note.isUnderEditting">{{note.phone}}</div>
            <div class="col-2" v-if="note.isUnderEditting"><input type="text" id="phone" class="form-control" v-model="phoneToEdit"></div>
            <div class="col-2" v-if="!note.isUnderEditting" ><div class="btn btn-warning" @click="onEdit(note.id)">Редактировать</div></div>
            <div class="col-2" v-if="note.isUnderEditting" ><div class="btn btn-warning" @click="onEditSave(note.id)">Сохранить</div></div>
            <div class="col-2" v-if="!note.isUnderEditting"><div class="btn btn-danger" @click="onDelete(note.id)">Удалить</div></div>
            <div class="col-2" v-if="note.isUnderEditting"><div class="btn btn-danger" @click="onEditCancel(note.id)">Отменить</div></div>
        </div>
    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        title: "Записная книжка",
        name: "",
        surname: "",
        phone: "",
        nameToEdit: "",
        surnameToEdit: "",
        phoneToEdit: "",
        isUnderEditting: false,
        notes: []
      };
    },
    methods: {
      onSave() {
        let note = {
            name: this.name,
            surname: this.surname,
            phone: this.phone,
            isUnderEditting: this.isUnderEditting
        };
        this.$http
            .post("http://localhost:3000/notes", note)
            .then(res => console.log(res))
            .then(() => this.onLoad());
      },
      onLoad() {
        this.$http
            .get("http://localhost:3000/notes")
            .then(res => res.json())
            .then(res => (this.notes = res));
      },
      onDelete(id) {
            console.log(id);
            for (let i = 0; i < this.notes.length; i++){
                if (this.notes[i].id == id){
                    this.notes.splice(i, 1);
                    this.$http
                      .delete("http://localhost:3000/notes/" + String(id))
                      .then(res => console.log(res))
                }
            } 
      },
      onEdit(id) {
            for (let i = 0; i < this.notes.length; i++){
                if (this.notes[i].id == id){
                    this.notes[i].isUnderEditting = true;
                    this.nameToEdit = this.notes[i].name;
                    this.surnameToEdit = this.notes[i].surname;
                    this.phoneToEdit = this.notes[i].phone;
                }
            }
      },
      onEditSave(id){
          for (let i = 0; i < this.notes.length; i++){
              if (this.notes[i].id == id){
                  this.notes[i].name = this.nameToEdit;
                  console.log(this.name);
                  this.notes[i].surname = this.surnameToEdit;
                  this.notes[i].phone = this.phoneToEdit;
                  this.notes[i].isUnderEditting = false;
                  this.$http
                    .put("http://localhost:3000/notes/" + String(id), this.notes[i])
                    .then(res => console.log(res))
                    .then(() => this.onLoad());
              }
          }
      },
      onEditCancel(id){
        for (let i = 0; i < this.notes.length; i++){
          if (this.notes[i].id == id){
            this.notes[i].isUnderEditting = false;
          }
        }
      }
  }
};
</script>
