<script>
import {Observable, interval} from 'rxjs'
import { onMount } from 'svelte';

let counter = 0;
let text = "";
let message$ = new Observable((observer) => {
    observer.next("Hello from Svelte and RxJS")
})

let counter$ = interval(1000)


onMount(() => {
    message$.subscribe(val => text = val)
    counter$.subscribe(val => counter = val)
})


</script>


<div class="container">
    <h1>{text}</h1>
    <h1>Counter is here: {counter}</h1>
</div>



<style>

</style>