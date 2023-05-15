<script lang="ts">
    import { onMount, onDestroy } from "svelte";
    import { Card, Button, Label, Input, Checkbox } from "flowbite-svelte";
    import { Table, TableBody, TableBodyCell, TableBodyRow, TableHead, TableHeadCell } from 'flowbite-svelte';


    let todos = [];
    let newTodo: string;
    let getTodoId: string;

    //on mount gathers all todos from db if any and displays them in loop below.
    onMount(async () => {
        await fetch(
            'http://localhost:8080/todos',
            {
                method: 'GET',
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                },
            }
        )
        .then(response => response.json())
        .then(result => todos = result.data);
    })
    // Post todo to server, then appends to end of todos array. 
    async function sendTodo() {
        fetch(
            'http://localhost:8080/todos',
            {
                method:  'POST',
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({
                    body: newTodo
                })
            })
            .then(response => response.json())
            .then(result => todos = [...todos, result.todo])
    }
    // Send delete call to the server, and removes the offending todo
</script>
<div class="mt-2 mb-4 bg-white p-4">
    <form on:submit|preventDefault={sendTodo}>
        <Input type="text" name="todo" placeholder="Todo" bind:value={newTodo} required />
        <Button type="submit" class="mt-2">Create Todo</Button>
    </form>
</div>
  
<Table striped={true}>
    <TableBody>
        {#each todos as todo (todo.ID)}
            <TableBodyRow>
            <TableBodyCell>{todo.Content}</TableBodyCell>
            <TableBodyCell></TableBodyCell>
            <TableBodyCell></TableBodyCell>
            <TableBodyCell></TableBodyCell>
            <TableBodyCell>
                <Button on:click="{async(e) => {
                    await fetch(`http://localhost:8080/todos/${todo.ID}`, {
                        method: 'DELETE'
                    });
                    todos = todos.filter((t) => t !== todo);
                }}">
                    Delete
                </Button>
            </TableBodyCell>
            </TableBodyRow>
        {/each}
    </TableBody>
</Table>