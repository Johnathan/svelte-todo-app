<script>
    export let todos;
</script>

<div>
    {#each todos as todo}
        <div>
            {todo.title} - {todo.id}
            {#if todo.dueDate}
                <small>{todo.dueDate}</small>
            {/if}
            <input type="checkbox" bind:checked={todo.done}>
        </div>
    {/each}
</div>
