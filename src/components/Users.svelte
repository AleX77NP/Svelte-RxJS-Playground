<script>

import {ajax} from 'rxjs/ajax'
import {from, of, Observable} from 'rxjs'
import {map, catchError, take, delay, mergeMap, concatMap} from 'rxjs/operators'
import { onMount } from 'svelte';

    let users = [];
    let url = `https://jsonplaceholder.typicode.com/users`

    const users$ = ajax.getJSON(url).pipe(
            take(1),
            catchError(err => {
                console.log(err)
            })
        )

    onMount(() => {
       // todo...
    })

    users$.subscribe(value => {
        let obs = new Observable(observer => {
            observer.next(value);
            observer.complete();
        }).pipe(
            mergeMap(x => from(x)),
            concatMap(x => of(x).pipe(delay(1000)))
        ).subscribe(x => {
            users = [...users,x]
        })
    })




</script>

<div class="container">
<h1>Users: </h1>
{#each users as user}
<div class="card" style="margin-bottom: 20px;">
    <div class="card-header">{user.name}</div>
    <div class="card-body">{user.email}</div>
</div>
{/each}
</div>


<style>

</style>