<script>

  


  export default {
    data() {
      return{
        noteName: "Новая заметка",
        noteText: "",
        screen: "home",
        allNotes: [{
          id: 0,
          name: "Первая заметка!",
          text: "Заметки в этом приложении выглядят так!"
        }],
        noteId: -1
      }
    },
    
    computed: {
      changeNoteNameToBase(){
        if(this.noteName.trim() == "") this.noteName = "Новая заметка"
        else this.noteName = this.noteName.trim()
      },
      setStartNoteName(){
        this.noteName = "Новая заметка"
        
      },
    },
    methods: {
      firstNote(){
        if(!localStorage.getItem('noteList')) localStorage.setItem('noteList', JSON.stringify(this.allNotes));
        else{
          this.getAllNotes()
        }
      },
      saveNote(){
        if(this.noteText){
          if(this.noteId < 0) this.createNewNote()
          else this.updateNote();
          localStorage.setItem('noteList', JSON.stringify(this.allNotes))
        }       
      },
      createNewNote(){
        let res = {
          id: this.allNotes.length,
          name: this.noteName,
          text: this.noteText,
        }
        this.allNotes.push(res)
      },
      updateNote(){
        this.allNotes.map(e => {
          if(e.id == this.noteId){
            e.name = this.noteName
            e.text = this.noteText
          }
        })
      },
      removeNote(id){
        this.allNotes.map((e, i) => {
          if(e.id == id){
            this.allNotes.splice(i, 1)
          }

        })
        localStorage.setItem('noteList', JSON.stringify(this.allNotes))
      },
      toggleScreens(id=-1){
        this.screen = this.screen == "note" ? "home" : "note";
        if (this.screen == "home"){
          this.getAllNotes();
          this.noteId = -1;
          this.noteName = "Новая заметка";
          this.noteText = "";
          
        } else{
          this.noteId = id;
          this.allNotes.map(e => {
            if(e.id == this.noteId){
              this.noteName = e.name;
              this.noteText = e.text;
            }
          })
        }
        
      },
      getAllNotes(){
        this.allNotes = localStorage.getItem('noteList') ? JSON.parse(localStorage.getItem('noteList')) : [];
      },
      getStrSlice(str, strLen=20){
        if(str.length > strLen){
          return `${str.substr(0,strLen)}...`
        } else return str
      },
      
      
    },
    mounted() {
      this.$nextTick(function () {
        this.firstNote();
      })
    }
  }
</script>

<template>
  <div class="wrapper" v-if="screen == 'note'">

    <div class="header">
      <div class="">
        <img src="./assets/imgs/free-icon-turn-back-3585896.png" alt="" @click="toggleScreens()">
        <input v-model="noteName" @load="setStartNoteName" @focusout="changeNoteNameToBase">
      </div>
      <img src="./assets/imgs/free-icon-diskette-747439.png" alt="save-icon" class="save-btn" @click="saveNote()">
    </div>

    <div class="content">
      <textarea v-model="noteText" name="note" id="noteField" placeholder="Ваш текст начинается здесь..."></textarea>
    </div>

  </div>


  <div  class="wrapper" v-else>

    <div class="header">
      <p>Заметки</p>
      <img src="./assets/imgs/free-icon-create-list-8975585.png" @click="toggleScreens()">
    </div>

    <div class="content_cards">
      <div class="note_card" v-for="note in allNotes" :key="note.id" @click="toggleScreens(note.id)">
        <div class="header_note">
          <p class="note_card__name">{{ getStrSlice(note.name) }}</p>
          <img src="./assets/imgs/free-icon-trash-bin-8568959.png" @click.stop="removeNote(note.id)">
        </div>
        <p class="note_card__text">{{ getStrSlice(note.text, 30) }}</p>
      </div>
    </div>
  </div>
</template>

<style scoped>
  .wrapper{
    padding: 30px;
    width: 70%;
    
  }
  .flex{
    display: flex;

  }

  .header{
    border-bottom: 2px solid #5a5a5a;
    padding: 30px;
    display: flex;
    justify-content: space-between;
    align-items:end;
    user-select: none;
  }
  .header input{
    font-size: 28px;
    margin-top: 30px;
    background: transparent;
    border: 0;
    color: #110813;
    outline: none;
    border-bottom: 1px solid transparent;
    transition: all 500ms ease;
  }
  .header p{
    font-size: 28px;
  }

  .header input:focus{
    border-bottom: 1px solid #c78100;
  }

  .header img{
    width: 30px;
    height: 30px;
    transition: all 200ms ease;
  }

  .header img:hover{
    cursor: pointer;
    width: 32px;
    height: 32px;
  }

  .header div{
    width: 35%;
    display: flex;
    justify-content: space-between;
    align-items: end;
  }

  .content{
    margin-top: 40px;
    width: 100%;
  }
  .content_cards{
    margin-top: 40px;
    width: 100%;
    display: flex;
    justify-content: start;
    flex-wrap: wrap;

  }
  .content textarea{
    padding: 0 30px;
    width: 100%;
    height: 500px;
    border: none;
    background: transparent;
    resize: none;
    font-size: 20px;
    font-family: 'Roboto', sans-serif;
  }
  .content textarea:focus{
    border: none;
    outline: none !important;
  }

  .content textarea::-webkit-scrollbar {
    width: .2em;
    border-radius: 25px;
  }

  .content textarea::-webkit-scrollbar-track {
      -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,0.3);
      border-radius: 25px;
  }

  .content textarea::-webkit-scrollbar-thumb {
    background-color: rgb(114, 94, 68);
    outline: 1px solid rgb(65, 49, 26);
    border-radius: 25px;
  }

  .note_card{
    cursor: pointer;
    width: 25%;
    height: 60px;
    display: flex;
    flex-wrap: wrap;
    padding: 15px 30px;
    border-radius: 25px;
    border: 1px solid rgb(65, 49, 26);
    flex-direction: column;
    justify-content: space-between;
    /* margin-bottom: 30px; */
    /* margin-right: 3.6%; */
    margin: 0 1.8% 3% 1.8%;
    transition: all 200ms ease;
  }
  .note_card:hover{
    background: #dfbe985d;
  }
  .header_note{
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
  .header_note img{
    width: 20px;
    height: 20px;
    transition: all 100ms ease;
    user-select: none;
  }
  .header_note img:hover{
    width: 22px;
    height: 22px;
  }
  .note_card__name{
    font-weight: bold;
    font-size: 20px;
  }
  .note_card__text{
    font-size: 16px;
  }
</style>
