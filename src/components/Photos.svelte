<script>

import {ajax} from 'rxjs/ajax'
import {from, of, Observable} from 'rxjs'
import {map, catchError, take, delay, mergeMap, concatMap} from 'rxjs/operators'
import { onMount, onDestroy } from 'svelte';

let photos = []

const photos$ = ajax.getJSON(`https://jsonplaceholder.typicode.com/photos`).pipe(
    catchError(err => console.log(err))
).subscribe(data => {
    let obs = new Observable(observer => {
        observer.next(data)
        observer.complete()
    }).pipe(
        mergeMap(t => from(t)),
        concatMap(t => of(t).pipe(delay(500)))
    ).subscribe(c => {
        photos = [...photos, c]
    })
})

onDestroy(() => {
    photos$.unsubscribe()
})

</script>


<div class="container">
{#each photos as photo}
<div class="card">
    <div class="card-body">
        <img src={photo.thumbnailUrl} alt="error-thumb" />
    </div>
</div>
{/each}
</div>


<style>

</style>