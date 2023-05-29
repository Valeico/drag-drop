<script setup>
import { reactive } from 'vue';
import InputNew from './components/InputNew.vue';
// Objetop de tableros
let boards = reactive([
  {
    id: 1,
    name: 'tablero 1',
    items: [
      {
        id: 1,
        title: 'Feature archivos',
        img: 'https://upload.wikimedia.org/wikipedia/commons/thumb/9/99/Unofficial_JavaScript_logo_2.svg/1200px-Unofficial_JavaScript_logo_2.svg.png'
      },
      {
        id: 2,
        title: 'Resolver bug'
      }
    ]
  },
  {
    id: 2,
    name: 'tablero 2',
    items: [
      {
        id: 3,
        title: 'Feature archivos 2'
      },
      {
        id: 4,
        title: 'Resolver bug 2'
      }
    ]
  }
]);

const handleNewItem = (text, board) => {
  board.items.push({
    id: Math.random(),
    title: text.value,
  })
}

const handleNewBoard = () => {
  const name = prompt('Name of the board');
  if(!!name) {
    boards.push({
      id: crypto.randomUUID(),
      name: name,
      items: []
    });
  }
}

const startDrag = (evt, board, item) => {
  evt.dataTransfer.setData('text/plain', JSON.stringify({boardId: board.id, itemId: item.id}));
}

const onDrop = (evt, dest) => {

  // Obtenemos los datos de transferencia
  const {boardId, itemId} = JSON.parse(evt.dataTransfer.getData('text/plain'));

  // Obtenemos los datos de origen
  const originBoard = boards.find(item => item.id === boardId);
  const originItem = originBoard.items.find(item => item.id === itemId);

  if(dest.id != originBoard.id){
    dest.items.push({...originItem}); 
    originBoard.items = originBoard.items.filter(item => item.id != originItem.id);
  }

  // console.log(originBoard.name, originItem.title);
}


</script>

<template>
  <h1>Drag and drop example</h1>
  <nav>
    <ul>
      <li><a href="#" @click="handleNewBoard">Create Board</a></li>
    </ul>
  </nav>

  <div class="boards-container">
    <div class="boards">
      <div 
      class="board" 
      v-for="board in boards" 
      :key="board.id" 
      @drop="onDrop($event, board)" 
      @dragover.prevent 
      @dragenter.prevent>
        <div>{{ board.name }}</div>
        <InputNew @on-new-item="(text) => handleNewItem(text, board)"></InputNew>
        <div class="items">
          <div class="item" v-for="item in board.items" :key="item.id" draggable="true" @dragstart="startDrag($event, board, item)">
            {{ item.title }}
          <img :src="item.img" style="width: 100px;">
          </div>
        </div>
      </div>
    </div>
  </div>

</template>

<style scoped>
.boards {
  display: flex;
  gap: 10px;
  /* border: 1px solid black; */
}

.board {
  background: #b3b3b3;
  padding: 10px;
  border: 1px solid black;
}

.items {
  margin-top: 10px;
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.item {
  border: 1px solid black;
  background-color: white;
  padding: 10px;
}

.item:hover {
  cursor: grab
}

.item:active {
  cursor: grabbing;
}

.item:target {
  cursor: grabbing;
}


</style>
