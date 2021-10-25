<script>
    import {v4 as uuid} from 'uuid';
    import List from './list.svelte';
    // import { setContext, getContext } from 'svelte';
    // setContext('todos', []);
    // let todos = getContext('todos')
    let todos = [];

    const newTodoTemplate = {
        title: null,
        dueDate: null,
    }

    let newTodo = Object.assign({}, newTodoTemplate);

    const addTodo = () => {
        if (newTodo.title) {
            if (newTodo.dueDate) {
                newTodo.dueDate = (new Date(Date.parse(newTodo.dueDate)))
            }

            todos = [Object.assign({id: uuid()}, newTodo), ...todos]
            newTodo = Object.assign({}, newTodoTemplate);
        }
    }

    $: dueOn = (date) => {
        return todos.filter(todo => {
            if (todo.dueDate) {
                const dueDate = todo.dueDate.toDateString();
                return date.toDateString() === dueDate;
            }

            return false;
        })
    }

    $: dueToday = () => {
        return dueOn(new Date());
    }

    $: dueTomorrow = () => {
        const tomorrow = new Date();
        tomorrow.setDate(tomorrow.getDate() + 1);
        return dueOn(tomorrow);
    }

    $: {
    	console.log([
			dueToday().map(todo => todo.id),
			...dueTomorrow().map(todo => todo.id),
		]);
	}

    $: other = () => {
        const excludedIds = [
			...dueToday().map(todo => todo.id),
			...dueTomorrow().map(todo => todo.id),
		]

		return todos.filter(todo => {
			return !excludedIds.includes(todo.id);
		});
    }

</script>

<main>
    <div class="due-soon">
        <h2>Due today</h2>
        <List todos={dueToday()}/>

        <h2>Due tomorrow</h2>
        <List todos={dueTomorrow()}/>
    </div>

    <hr>

    <List todos={other()}/>

    <input type="text" bind:value={newTodo.title} on:keyup={e => {
		if (e.keyCode === 13) {
			addTodo();
		}
	}}/>
    <input type="datetime-local" bind:value={newTodo.dueDate}>
    <button disabled={!newTodo.title} on:click={() => {
		addTodo()
	}}>Add todo
    </button>
</main>

<style>
    .due-soon {
        background: pink;
    }
</style>
