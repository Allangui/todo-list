<template>
  <main>
    <header>
      <h1>todo</h1>
      <svg xmlns="http://www.w3.org/2000/svg" v-if="!lightMode" @click="toggleMode()" width="26" height="26"><path fill="#FFF" fill-rule="evenodd" d="M13 21a1 1 0 011 1v3a1 1 0 11-2 0v-3a1 1 0 011-1zm-5.657-2.343a1 1 0 010 1.414l-2.121 2.121a1 1 0 01-1.414-1.414l2.12-2.121a1 1 0 011.415 0zm12.728 0l2.121 2.121a1 1 0 01-1.414 1.414l-2.121-2.12a1 1 0 011.414-1.415zM13 8a5 5 0 110 10 5 5 0 010-10zm12 4a1 1 0 110 2h-3a1 1 0 110-2h3zM4 12a1 1 0 110 2H1a1 1 0 110-2h3zm18.192-8.192a1 1 0 010 1.414l-2.12 2.121a1 1 0 01-1.415-1.414l2.121-2.121a1 1 0 011.414 0zm-16.97 0l2.121 2.12A1 1 0 015.93 7.344L3.808 5.222a1 1 0 011.414-1.414zM13 0a1 1 0 011 1v3a1 1 0 11-2 0V1a1 1 0 011-1z"/></svg>
      <svg xmlns="http://www.w3.org/2000/svg" v-else @click="toggleMode()" width="26" height="26"><path fill="#FFF" fill-rule="evenodd" d="M13 0c.81 0 1.603.074 2.373.216C10.593 1.199 7 5.43 7 10.5 7 16.299 11.701 21 17.5 21c2.996 0 5.7-1.255 7.613-3.268C23.22 22.572 18.51 26 13 26 5.82 26 0 20.18 0 13S5.82 0 13 0z"/></svg>
    </header>
    <div class="card">
      <div class="topCard">
        <input type="checkbox" id="newTodo" name="newTodo" @click="addNewTodo()"  >
        <label for="newTodo"><input type="text" id="textNewTodo" @keyup.enter="addNewTodo()" v-model="theNewTodo" name="textNewTodo" placeholder="Create a new todo..." ></label>
      </div>
      <div class="mainCard">
          <draggable class="draggable" :list="todos">
            <div class="itemList" v-for="(todo,index) in todos" :key="todo.id" v-show="showFilter(todo)" >
            <input type="checkbox" class="todo todoIsDone" @change="todoLeftFunc(index)" v-model="todo.checked" name="todo">
            <label for="todo" @click="todo.checked? todo.checked=false : todo.checked=true, todoLeftFunc(index)"  >{{ todo.content  }}</label>
            <svg @click="deleteTodo(index)" xmlns="http://www.w3.org/2000/svg" width="18" height="18"><path fill="#494C6B" fill-rule="evenodd" d="M16.97 0l.708.707L9.546 8.84l8.132 8.132-.707.707-8.132-8.132-8.132 8.132L0 16.97l8.132-8.132L0 .707.707 0 8.84 8.132 16.971 0z"/></svg>
            </div>
            </draggable>
            <div class="lastItemList">
              <span class="left">{{ itemsLeft }} items left</span>
              <span class="all displayNoneMobile" :class="{isActive: whichFilter==='all'}" @click="filter('all')">All</span>
              <span class="active displayNoneMobile" :class="{isActive: whichFilter==='active'}" @click="filter('active')">Active</span>
              <span class="completed displayNoneMobile" :class="{isActive: whichFilter==='completed'}" @click="filter('completed')">Completed</span>
              <span @click="clearCompleted()" class="clearCompleted">Clear completed</span>
            </div>
      </div>
      <div class="bottomCard displayNoneDesk">
        <span class="all" :class="{isActive: whichFilter==='all'}" @click="filter('all')">All</span>
        <span class="active" :class="{isActive: whichFilter==='active'}" @click="filter('active')">Active</span>
        <span class="completed" :class="{isActive: whichFilter==='completed'}" @click="filter('completed')">Completed</span>
      </div>
    </div>
    <span class="textDrag">Drag and drop to reorder list</span>
  </main>
</template>

<script >
import { VueDraggableNext } from 'vue-draggable-next'
export default{
    components: {
        draggable: VueDraggableNext,
      },
    data() {
      return {
        todos: [],
        theNewTodo:'',
        itemsLeft:0,
        whichFilter:'all',
        lightMode:false,
        body : document.body
      }
  },
  methods: {
    addNewTodo() {
      if (this.theNewTodo.length >0 && !this.theNewTodo.includes(' ',0)){
      this.todos.unshift({content:this.theNewTodo, checked:false})
      this.theNewTodo = ''
      this.itemsLeft += 1
      }else{
        console.log('Espace vide')
      }
    },
    deleteTodo(id){
      if(this.todos[id].checked===false){
      this.itemsLeft -= 1
      } else {
        null
      }
      this.todos.splice(id,1)
    },
    todoLeftFunc(id) {
      if(this.todos[id].checked===true){
        this.itemsLeft -= 1
      }else{
        this.itemsLeft += 1
      }
    },
    filter(value){
      if(value==='all'){
         this.whichFilter='all'
      }else if(value==='active'){
         this.whichFilter='active'
      }else if(value==='completed'){
         this.whichFilter='completed'
      }
    },
    showFilter(todo){
      if(this.whichFilter==='all'){
        return true
      } else if(this.whichFilter==='active'){
        if(!todo.checked){
          return true
        } else {
          return false
        }
      }else if(this.whichFilter==='completed'){
        if(todo.checked){
          return true
        } else {
          return false
        }
      }
    },
    clearCompleted(){
      for(let i=0;i< this.todos.length ; i++){
        if (this.todos[i].checked){
          this.todos.splice(i,1)
          i--
        }else{
        }
      }
    },
    toggleMode() {
      if(!this.lightMode){
        this.lightMode = true
        document.body.classList.replace('BGdark','BGlight')
        document.documentElement.style.setProperty('--bgColor', 'hsl(236, 33%, 92%)')
        document.documentElement.style.setProperty('--bgTodo', 'hsl(0, 0%, 98%)')
        document.documentElement.style.setProperty('--fontColor', 'hsl(235, 19%, 35%)')
        document.documentElement.style.setProperty('--borderColor', 'hsl(236, 33%, 92%)')
        document.documentElement.style.setProperty('--fontComplete', 'hsl(233, 11%, 84%)')
        document.documentElement.style.setProperty('--fontLight', 'hsl(236, 9%, 61%)')
        document.documentElement.style.setProperty('--fontHover', 'hsl(235, 19%, 35%)')
      } else  {
        this.lightMode = false
        document.body.classList.replace('BGlight','BGdark')
        document.documentElement.style.setProperty('--bgColor', 'hsl(235, 21%, 11%)')
        document.documentElement.style.setProperty('--bgTodo', 'hsl(235, 24%, 19%)')
        document.documentElement.style.setProperty('--fontColor', 'hsl(234, 39%, 85%)')
        document.documentElement.style.setProperty('--borderColor', 'hsl(237, 14%, 26%)')
        document.documentElement.style.setProperty('--fontComplete', 'hsl(233, 14%, 35%)')
        document.documentElement.style.setProperty('--fontLight', 'hsl(233, 14%, 35%)')
        document.documentElement.style.setProperty('--fontHover', 'hsl(236, 33%, 92%)')
      }
    },
  },
  mounted () {
    window.setTimeout(() => {
      this.body.style.transition= "background-color .5s,background-image .5s,color .5s"
    }, 100);
  },
}
</script>
<style lang="scss">
:root{
  --bgColor:hsl(235, 21%, 11%);
  --bgTodo:hsl(235, 24%, 19%);
  --fontColor:hsl(234, 39%, 85%);
  --borderColor:hsl(237, 14%, 26%);
  --fontComplete:hsl(233, 14%, 35%);
  --fontLight:hsl(233, 14%, 35%);
  --fontHover:hsl(236, 33%, 92%);
}
.BGlight{
background-image: url(../src/assets/bg-mobile-light.jpg);
}
.BGdark{
background-image: url(../src/assets/bg-mobile-dark.jpg);
}
body {
  
  font-size: 18px;
  background-repeat: no-repeat;
  background-size:100%;
  background-color: var(--bgColor);
  font-family: 'Josefin Sans', sans-serif;
  color:var(--fontColor);

  #app{
    max-width: 85vw;
    margin:50px auto;
    
  }
  input{
    outline: none;
  }
}
@media only screen and (min-width: 700px) {
  .BGlight{
    background-image: url(../src/assets/bg-desktop-light.jpg);
  }
  .BGdark{
    background-image: url(../src/assets/bg-desktop-dark.jpg);
  }
  
}
</style>
<style scoped lang="scss">


main{
  max-width:500px;
  margin:0 auto;
    header{
    display: flex;
    justify-content: space-between;
    margin-bottom: 30px;
    h1{
      font-size: 1.8rem;
      font-weight: 700;
      letter-spacing: 6px;
      text-transform: uppercase;
      color:white;
    }
    svg{
      cursor: pointer;
    }
  }
  .card{
      .topCard{
      width:100%;
      background-color: var(--bgTodo);
      padding:15px;
      border-radius: 5px;
      display: flex;
      align-items: center;
      margin-bottom: 20px;
      transition: color .5s,background-color .5s,border .5s;
        #newTodo{
          -webkit-appearance: none;
          -moz-appearance: none;
          -ms-appearance: none;
          height:25px;
          width:25px;
          border: 1px solid var(--borderColor);
          border-radius: 50%;
          background-color: transparent;
          cursor: pointer;
          transition: color .5s,background-color .5s,border .5s;
        }

        label{
          width:80%;
          
        }
        #textNewTodo{
          background-color: transparent;
          border-color: transparent;
          font-family: 'Josefin Sans', sans-serif;
          margin-left:5px;
          width:100%;
          font-size: .9rem;
          color:var(--fontColor);
        }
      }
    }
    .mainCard{
-webkit-box-shadow: -3px 9px 50px 2px rgba(0,0,0,0.1); 
box-shadow: -3px 9px 50px 2px rgba(0,0,0,0.1);

      .draggable{
        .itemList{
          transition: color .5s,background-color .5s,border .5s;
          &:first-child{
            border-top-right-radius: 5px;
            border-top-left-radius: 5px;
          }
          width:100%;
          background-color: var(--bgTodo);
          padding:10px 15px;
          border-bottom:solid 1px var(--borderColor);
          display: flex;
          align-items: center;
          justify-content: space-between;
          font-size: 1rem;
          } 
          .todo{
          -webkit-appearance: none;
          -moz-appearance: none;
          -ms-appearance: none;
          height:25px;
          width:25px;
          border: 1px solid var(--borderColor);
          border-radius: 50%;
          background-color: transparent;
          cursor: pointer;
          transition: color .5s,background-color .5s,border .5s;
          }
          .todo:checked{
          background: linear-gradient(hsl(192, 100%, 67%) , hsl(280, 87%, 65%));
          position: relative;
            &:before {
              content: '';
              background: no-repeat url(../src/assets/icon-check.svg);
              width:15px;
              height:15px;
              top:30%;
              left:30%;
              display: block;
              position: absolute;
            }
          }
          label{
          background-color: transparent;
          border-color: transparent;
          font-family: 'Josefin Sans', sans-serif;
          margin-left:5px;
          font-size: 0.9rem;
          width:80%;
          padding:10px 0;
          cursor: pointer;
          }
          svg{
          cursor: pointer;
          }
          .todoIsDone:checked + label{
            text-decoration:line-through;
            color:var(--fontComplete);
            }
          }
        }
        .lastItemList{
          color:var(--fontLight);
          border:none;
          border-bottom-left-radius: 5px;
          border-bottom-right-radius: 5px;
          
          width:100%;
          background-color: var(--bgTodo);
          padding: 15px;
          display: flex;
          align-items: center;
          justify-content: space-between;
          font-size: 1rem;
          transition: color .5s,background-color .5s,border .5s;
          
          .displayNoneMobile{
            display: none;
          }
          .clearCompleted{
            cursor: pointer;
            &:hover{
              color:var(--fontHover)
            }
          }
        }
    
    .bottomCard{
      width:100%;
      margin-top:20px;
      background-color: var(--bgTodo);
      color: var(--fontLight);
      font-weight: 700;
      padding:20px;
      border-radius: 5px;
      display: flex;
      align-items: center;
      justify-content: center;
      column-gap: 25px;
      font-size: 1rem;
      -webkit-box-shadow: -3px 9px 50px 2px rgba(0,0,0,0.1); 
      box-shadow: -3px 9px 50px 2px rgba(0,0,0,0.1);
      transition: color .5s,background-color .5s,border .5s;
      span{
        cursor: pointer;
        &:hover{
          color:var(--fontHover);
        }
      }
      .isActive{
        color:hsl(220, 98%, 61%)!important;
      }
    }
      .textDrag{
        color:var(--fontComplete);
        text-align: center;
        display: inline-block;
        margin-top:50px;
        width: 100%;
        transition: color .5s;
      }
}
@media only screen and (min-width: 700px) {
  .displayNoneMobile{
    display:block !important;
  }
.displayNoneDesk{
  display: none !important;
}
.mainCard{
  .lastItemList{
    font-size: .9rem !important;
      .isActive{
        color:hsl(220, 98%, 61%)!important;
      }
      .left{
        margin-right:40px;
      }
        .all,.active,.completed{
        cursor: pointer;
        &:hover{
          color:var(--fontHover);
        }
      }
      .clearCompleted{
        cursor: pointer;
        margin-left:30px;
        &:hover{
          color:var(--fontHover);
        }
      }
    }
  }
}
</style>


