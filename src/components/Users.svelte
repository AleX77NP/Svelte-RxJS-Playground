<script>
import {ajax} from 'rxjs/ajax'
import {from, of, Observable, merge, combineLatest, concat, zip} from 'rxjs'
import {map, catchError, take, delay, mergeMap, concatMap} from 'rxjs/operators'
import { onDestroy, onMount } from 'svelte';

   const weight = of(10,20,30,40);
   const height = of(1,2,3,4);

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

    const bmi = concat(weight, height).pipe();

    bmi.subscribe(x => console.log(x));

    // combine users and albums

    const usersStream$ = users$.subscribe(value => {
        let obs = new Observable(observer => {
            observer.next(value);
            observer.complete();
        }).pipe(
            mergeMap(x => from(x)),
            concatMap(x => of(x).pipe(delay(1000)))
        )
    })

    const albumsStream$ = albums$.subscribe(value => {
        let obs = new Observable(observer => {
            observer.next(value);
            observer.complete();
        }).pipe(
            mergeMap(x => from(x)),
            concatMap(x => of(x).pipe(delay(1000)))
        )
    })


    zip(users$, albums$).pipe(
        map(([x,y]) => {
            let custom = [];
            x.forEach(elem => {
                y.forEach(item => {
                    if (elem.id == item.userId)
                    custom.push({name: elem.username, album: item.title})
                })
            })
            return custom
        })
    ).subscribe(v => users = v)


    onDestroy(() => {
        //...
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