<script>
    import {authHandlers, authStore} from "../../store/store.js";
    import {db} from "$lib/firebase/firebase.js";
    import {doc, setDoc} from "firebase/firestore";

    let todoList = []
    let currTodo = ''

    authStore.subscribe(curr => {
        todoList = curr.data.todos
    })

    const addTodo = () => {
        if (!currTodo) return
        todoList = [...todoList, currTodo]
        currTodo = ''
    }

    const editTodo = (index) => {
        let newTodoList = todoList.filter((val, i) => {
            return i !== index
        })

        currTodo = todoList[index]
        todoList = newTodoList
    }

    const removeTodo = (index) => {
        todoList = todoList.filter((val, i) => {
            return i !== index
        })
    }

    const saveTodos = async () => {
        try {
            const userRef = doc(db, 'users', $authStore.user.uid)
            await setDoc(userRef, {todos: todoList}, {merge: true})
        }
        catch (error) {
            console.log("there was an error saving your information", error)
        }
    }
</script>

<div class="dashboard">
    {#if !$authStore.loading}

        <div class="header">
            <h1>Todo List</h1>
            <button on:click={saveTodos}>Save</button>
            <button on:click={authHandlers.logout}>Logout</button>
        </div>
        <main>
            {#if todoList.length === 0}
                <p>You have nothing to do.</p>
            {/if}
            {#each todoList as todo, index}
                <div class="todo">
                    <p> {index + 1}. {todo} </p>
                    <div>
                        <p on:click={() => editTodo(index)} class="pointer">edit</p>
                        <p on:click={() => removeTodo(index)} class="pointer">delete</p>
                    </div>
                </div>
            {/each}
        </main>
        <div class="enter-todo">
            <input bind:value={currTodo} type="text" placeholder="Enter todo">
            <button on:click={addTodo}>ADD</button>
        </div>
    {/if}
</div>

<style>
    .dashboard {
        display: flex;
        flex-direction: column;
        min-height: 100vh;
        width: 100%;
        max-width: 1000px;
        margin: 0 auto;
        gap: 24px;
    }

    .header {
        display: flex;
        align-items: center;
        justify-content: space-between;
    }

    .header button {
        background: #003c5b;
        color: white;
        padding: 10px 18px;
        border: none;
        border-radius: 4px;
        font-weight: 700;
        display: flex;
        align-items: center;
        gap: 8px;
    }

    .header button:hover {
        opacity: .7;
        cursor: pointer;
    }

    main {
        display: flex;
        flex-direction: column;
        gap: 8px;
        flex: 1;
    }

    .todo {
        display: flex;
        justify-content: space-between;

        border-bottom: 2px solid #241c46;
    }

    .pointer {
        cursor: pointer;
    }

    .pointer:hover {
        border-bottom: 1px solid aqua;
    }

    .enter-todo {
        display: flex;
        align-items: stretch;
        border: 1px solid #0891b2;
        border-radius: 4px;
        overflow: hidden;
    }

    .enter-todo input {
        background: transparent;
        border: none;
        padding: 14px;
        color: white;
        flex: 1;
    }

    .enter-todo input:focus {
        outline: none;
    }

    .enter-todo button {
        padding: 0 14px;
        background: #003c5b;
        border: none;
        color: cyan;
        font-weight: 600;
        cursor: pointer;
    }

    .enter-todo button:hover {
        background: transparent;
    }
</style>