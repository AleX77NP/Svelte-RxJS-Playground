<script>
import {ajax} from 'rxjs/ajax'
import {from, of, Observable, merge} from 'rxjs'
import {map, catchError, take, delay, mergeMap, concatMap} from 'rxjs/operators'
import { onDestroy, onMount } from 'svelte';

    let users = [];
    let url = `https://jsonplaceholder.typicode.com/users`

    const users$ = ajax.getJSON(url).pipe(
            take(1),
            catchError(err => {
                console.log(err)
            })
        )
    const albums$ = ajax.getJSON(`https://jsonplaceholder.typicode.com/albums`).pipe(
            take(1),
            catchError(err => {
                console.log(err)
            })
        )

    onMount(() => {
       // todo...
    })

    // combine users and albums

    const usersAlbumsStream$ = users$.subscribe(value => {
        let obs = new Observable(observer => {
            observer.next(value);
            observer.complete();
        }).pipe(
            mergeMap(x => from(x)),
            concatMap(x => of(x).pipe(delay(200)))
        ).subscribe(x => {
            albums$.pipe(
                mergeMap(z => from(z)),
                concatMap(z => of(z).pipe(delay(1000))),
                map(z => ({name: x.name, album: z.title}))
            )
            .subscribe(v => {
                users = [...users, v]
            })
        })
    })

    onDestroy(() => {
        usersAlbumsStream$.unsubscribe()
    })




</script>

<div class="container">
<h1>Users: </h1>
{#each users as user}
<div class="card" style="margin-bottom: 20px;">
    <div class="card-header">{user.name}</div>
    <div class="card-body">{user.album}</div>
</div>
{/each}
</div>


<style>

</style>