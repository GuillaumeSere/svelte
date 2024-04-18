<script>
	import Header from './Header.svelte';
	import './styles.css';
    import github from '$lib/images/github.svg';
    import { crossfade, scale } from 'svelte/transition';
	import images from './images.js';

    const [send, receive] = crossfade({
		duration: 200,
		fallback: scale
	});

	let selected = null;
	let loading = null;

	const ASSETS = `../src/lib/images`;

	const load = (image) => {
		const timeout = setTimeout(() => (loading = image), 100);

		const img = new Image();
        console.log(image)

		img.onload = () => {
			selected = image;
			clearTimeout(timeout);
			loading = null;
		};

		img.src = `${ASSETS}/` + "image"  + `${image.id}.png`;
	};

</script>

<div class="app">
	<Header />
    <slot />
	<main>
        <div class="container">
            <div class="phone">
        
                  <!-- svelte-ignore a11y-click-events-have-key-events a11y-no-noninteractive-element-interactions -->
                <div class="grid">
                    {#each images as image}
                        <div class="square">
                            {#if selected !== image}
                                <button
                                    style="background-color: {image.color};"
                                    on:click={() => load(image)}
                                    in:receive={{ key: image.id }}
                                    out:send={{ key: image.id }}>{loading === image ? '...' : ' '}</button
                                >
                            {/if}
                        </div>
                    {/each}
                </div>
        
                {#if selected}
                    {#await selected then d}
                        <div class="photo" in:receive|global={{ key: d.id }} out:send|global={{ key: d.id }}>
                            <!-- svelte-ignore a11y-click-events-have-key-events a11y-no-noninteractive-element-interactions -->
                            <img alt={d.alt} src="{ASSETS}/image{d.id}.png" on:click={() => (selected = null)} />
        
                            <p class="credit">
                                <a target="_blank" rel="noreferrer" href="https://www.flickr.com/photos/{d.path}"
                                    >via Flickr</a
                                >
                                &ndash;
                                <a target="_blank" rel="noreferrer" href={d.license.url}>{d.license.name}</a>
                            </p>
                        </div>
                    {/await}
                {/if}
            </div>
        </div>
	</main>

	<footer>
        <picture>
            <a href="https://github.com/GuillaumeSere" target="_blank"><img class="github" src={github} alt="github"></a>
        </picture>
	</footer>
</div>

<style>
	.app {
		display: flex;
		flex-direction: column;
		min-height: 100vh;
	}

	main {
		flex: 1;
		display: flex;
		flex-direction: column;
		padding: 1rem;
		width: 100%;
		max-width: 64rem;
		margin: 0 auto;
		box-sizing: border-box;
	}

    .container {
		position: absolute;
		display: flex;
		align-items: center;
		justify-content: center;
		width: 100%;
		height: 100%;
		top: 8rem;
		left: 0;
	}

	.phone {
		position: relative;
		display: flex;
		flex-direction: column;
		width: 52vmin;
		height: 55vmin;
		border: 2vmin solid #ccc;
		border-bottom-width: 10vmin;
		padding: 3vmin;
		border-radius: 2vmin;
	}

    .grid {
		display: grid;
		flex: 1;
		grid-template-columns: repeat(3, 1fr);
		grid-template-rows: repeat(4, 1fr);
		grid-gap: 2vmin;
	}

	button {
		width: 100%;
		height: 100%;
		color: white;
		font-size: 5vmin;
		border: none;
		margin: 0;
		will-change: transform;
	}

	footer {
		display: flex;
		justify-content: center;
		align-items: center;
		padding: 12px;
	}
    .github{
        position: absolute;
        bottom: -5rem;
        display: flex;
        justify-content: center;
        width: 50px;
        height: 50px;
        padding-bottom: 0.2rem;
    }
    .photo,
	img {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		overflow: hidden;
	}

	.photo {
		display: flex;
		align-items: stretch;
		justify-content: flex-end;
		flex-direction: column;
		will-change: transform;
	}

	img {
		object-fit: cover;
		cursor: pointer;
	}

	.credit {
		text-align: right;
		font-size: 2.5vmin;
		padding: 1em;
		margin: 0;
		color: white;
		font-weight: bold;
		opacity: 0.6;
		background: rgba(0, 0, 0, 0.4);
	}

	.credit a,
	.credit a:visited {
		color: white;
	}

	@media (min-width: 480px) {
		footer {
			padding: 12px 0;
		}
	}
</style>
