<script>
	import { initializeApp } from "firebase/app";
	import { getFirestore, collection, onSnapshot, doc, updateDoc, deleteDoc,addDoc } from "firebase/firestore"
	import {firebaseConfig} from '../lib/firebaseConfig';
	import "./style.css";


	// Initialize Firebase
	const firebaseApp =  initializeApp(firebaseConfig)
	// Initialize Cloud Firestore and get a reference to the service
	const db = getFirestore(firebaseApp);

	const colRef = collection(db, "todos");

	let todos = []

	const unsubscribe = onSnapshot(colRef, (querySnapshot) => {
		let fbTodos = []
		querySnapshot.forEach((doc) => {
			let todo = {...doc.data(), id: doc.id}
			fbTodos = [todo, ...fbTodos]
		});
		todos = fbTodos;
	});

	let task = "";

	const createTodo = async () =>{
		if(task){
			// Add a new document in collection "cities"
			await addDoc(collection(db, "todos"), {
				task: task,
				createdAt: new Date(),
				completed: false
			});

			task = "";
		}
	}
	const markAsCompleteTodo = async (item) =>{
		const updateTodo = doc(db, "todos",item.id);

		// Set the "capital" field of the city 'DC'
		await updateDoc(updateTodo, {
			completed: !item.completed
		});
	}

	const deleteTodo = async (item) =>{
		// todos.splice(index,1);
		//we need to reasign itself again with an updated value so that it will run again and
		//be updated on the DOM
		// todos = todos

	//	second method using filter
	// 	let deletedTodo = todos[index];
	// 	todos = todos.filter(todo => todo !== deletedTodo);

		await deleteDoc(doc(db, "todos", item.id));

	}

	const keyIsPressed = (e) =>{
		//if task is not empty and when press enter
		if(e.key === "Enter" && task){
			createTodo()
		}
	}

</script>

<section class='main-todo'>
	<div class='add-todo-area-input'><input type='text' placeholder='Add your todos..' bind:value={task}> <button class="add-task-btn" on:click={createTodo}>+</button></div>

	<ol>
		{#each todos as item}
			<li>
				<span class:completed={item.completed} class='task-name'>{item.task}</span>
				<div>
					<button class="complete-task-btn" on:click={()=>markAsCompleteTodo(item)}>✓</button>
					<button class="delete-task-btn" on:click={()=>deleteTodo(item)}>✖</button>
				</div>
			</li>

		{:else}
			<p>No todos</p>
		{/each}
	</ol>
</section>

<svelte:window on:keydown={keyIsPressed}/>

<style>

	.completed{
			text-decoration: line-through;
			color: #979797 !important;
	}
	.main-todo{
			width: 500px;
			min-width: 320px;
	}
	li{
			display: flex;
			justify-content: space-between;
			align-items: center;
	}

	.add-todo-area-input{
			display: flex;
			justify-content: space-between;
			align-items: center;
			gap: 20px;
			margin-bottom: 20px;
	}
	input{
			width: 90%;
			outline: none;
			border: none;
      background: #f5f5f5;
			color: #454545;
			padding: 10px 20px;
			border-radius: 20px;
			box-shadow: 0 2px 5px rgba(0,0,0,.2);
	}

	.add-task-btn{
			color: #fff;
			border-radius: 100%;
			display: flex;
			justify-content: center;
			align-items: center;
			width: 36px;
			height: 36px;
			background-color: #5459F2;
			border: none;
			outline: none;
      cursor: pointer;
			font-size: 20px;
	}
	.add-task-btn:hover{
      background-color: #474bd7;
	}

  ol{
			display: flex;
			flex-direction: column;
			justify-content: space-between;
			align-items: center;
      padding: 10px 0;
			gap:10px;
	}
	ol li{
			width: 100%;
			padding: 5px 10px;
			border-radius: 10px;
      box-shadow: 0 2px 5px rgba(0,0,0,.2);
	}
	.task-name{
      color: #454545;
			font-size: 18px;
			user-select: none;
	}
	.complete-task-btn,.delete-task-btn{
			padding: 2px 7px;
			border: none;
			outline: none;
			background-color: transparent;
			cursor: pointer;
	}
	.complete-task-btn{
			border:1px solid cadetblue;
			border-radius: 10px;
			font-weight: bold;
      user-select: none;
	}
	.delete-task-btn{
      border:1px solid #a05f5f;
      border-radius: 10px;
      font-weight: bold;
      user-select: none;
	}
</style>