<script>
    let prompt = "";
    let messages = [];

    async function sendPrompt() {
        const body = {
            model: "gpt-3.5-turbo",
            messages: [{ role: "user", content: prompt }],
        };
        const response = await fetch(
            "https://api.openai.com/v1/chat/completions",
            {
                method: "POST",
                headers: {
                    "Content-Type": "application/json",
                },
                body: JSON.stringify(body),
            },
        );
        if (response.ok) {
            const data = await response.json();
            const message = data.choices[0].message.content;
            messages = [...messages, message];
        } else {
            console.error("Failed to fetch data:", response.statusText);
        }
    }
</script>

<h1>SvelteGPT</h1>

<form method="POST" on:submit|preventDefault="{sendPrompt}">
    <label for="prompt">
        Prompt:
        <textarea name="prompt" rows="4" bind:value="{prompt}"></textarea>
    </label>
    <button type="submit" on:keydown="{sendPrompt}">Send</button>
</form>

{#each messages as message}
    <div>
        <!-- {#await messages_var then message} -->
        <pre>{message}</pre>
        <!-- {/await} -->
    </div>
{/each}
