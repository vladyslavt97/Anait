<script>
    import { SyncLoader } from "svelte-loading-spinners";
    import { onMount } from "svelte";
    import { Motion } from "svelte-motion";
    import { theLanguage } from "../../store/store";
    let currentLanguage = "g";
    theLanguage.subscribe((value) => {
        currentLanguage = value;
    });
    let theConcerts = [
        { date: "stillLoading", location: "", programme: "", url: "" },
    ];
    onMount(async () => {
        try {
            const response = await fetch("/api/getconcerts");
            if (response.ok) {
                theConcerts = await response.json();
            } else {
                throw new Error("Failed to fetch data");
            }
        } catch (error) {
            console.error("error", error);
        }
    });
</script>

<div
    class="flex flex-col justify-center items-center gap-10 pb-10 font-sans min-h-screen sm:min-h-[98.5vh]"
>
    {#if theConcerts[0].date === "stillLoading"}
        <p class="">
            <SyncLoader color="#ff9500" />
        </p>
    {:else}
        <Motion
            initial={{ opacity: 0 }}
            animate={{ opacity: 1 }}
            transition={{ duration: 2, delay: 1 }}
            let:motion
        >
            <h1 use:motion class="font-bold text-center relative top-20">
                {#if currentLanguage === "e"}
                    Concerts
                {:else}
                    Konzerte
                {/if}
            </h1>
        </Motion>
        <div class="relative top-16 mx-10">
            {#each theConcerts as concert}
                <Motion
                    initial={{ opacity: 0 }}
                    animate={{ opacity: 1 }}
                    transition={{ duration: 2 }}
                    let:motion
                >
                    <div
                        class="rounded-2xl border drop-shadow-lg p-2 mb-5 flex flex-col items-center"
                        use:motion
                    >
                        <h2 class="font-bold text-yellow-400">
                            {concert.date}
                        </h2>
                        <h3 class="italic">{concert.location}</h3>
                        {#each concert.programme as programItem}
                            {#if programItem !== ""}
                                <li class="text-sm">{programItem}</li>
                            {:else}
                                <p />
                            {/if}
                        {/each}
                        <br />
                        {#if concert.url}
                            <Motion
                                initial={{ opacity: 0 }}
                                animate={{ opacity: 1 }}
                                transition={{ duration: 2 }}
                                let:motion
                            >
                                <img
                                    use:motion
                                    src={concert.url}
                                    alt="poster"
                                    class="rounded-lg max-h-96"
                                    loading="eager"
                                />
                            </Motion>
                        {/if}
                    </div>
                </Motion>
            {/each}
            <br />
            <br />
            <br />
        </div>
    {/if}
</div>
