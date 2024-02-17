<script>

    import VisApp from '../components/VisApp.svelte';
    import jsonData from '../components/Per-Capita-Energy-USE.json';
    
     
</script>

<main>
    <h1>Data Visualization Project</h1>

    <section class="Part1">
        <VisApp {jsonData} />
    </section>

</main>


<style>
    @import url('https://fonts.googleapis.com/css2?family=Nunito:wght@300;400;700&display=swap');

    :root {
        --color-bg: #9EECFF;
        --color-outline: #B3F0FF;
        --color-shadow: hsla(7, 100%, 50%, 0.5);
        --color-text: #443408;
    }

    *,
    *::before,
    *::after {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }

    main {
        height: 100%;
        display: grid;
        place-content: center;
        text-align: center;
        font-family: 'Nunito', sans-serif;
        font-weight: 300;
        line-height: 2;
        font-size: 24px;
        color: var(--color-text);
    }


    input,
    button {
        font-family: inherit;
        font-weight: inherit;
        line-height: inherit;
        font-size: 24px;
        width: 100%;
    }

    h1 {
        font-size: 72px;
        font-weight: 300;
        line-height: 2;
    }

    
    
</style>

