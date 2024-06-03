<script lang="js">
    let prompt = "";
    let messages = [];

    async function sendPrompt() {
        const body = {
            model: "Codestral-22B-v0.1-hf-Q5_K_M",
            messages: [{ role: "user", content: prompt }],
        };
        const response = await fetch(
            "https://0x57fa64fb75d1b8c778063adcd81d99e525b6197d.gaianet.network/v1/chat/completions",
            {
                method: "POST",
                headers: {
                    "Content-Type": "application/json",
                    Authorization: `fake_token`,
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

    //when I click on the save button, save doesn't work, pls check
    function saveMessages() {
        let textToSave = "";

        messages.forEach(({ prompt, response }) => {
            textToSave += `Prompt:\n${prompt}\n\nResponse:\n${response}\n\n---\n\n`;
        });

        const blob = new Blob([textToSave], { type: "text/plain" });

        const linkElement = document.createElement("a");
        linkElement.download = "prompts_and_responses.txt";
        linkElement.href = window.URL.createObjectURL(blob);

        document.body.appendChild(linkElement);

        linkElement.click();

        document.body.removeChild(linkElement);
    }

    function updateMessagesUI() {
        const container = document.getElementById("messages-container");
        container.innerHTML = ""; // Clear previous contents

        messages.forEach(({ prompt, response }) => {
            const divElement = document.createElement("div");
            divElement.innerHTML =
                `<pre><strong>Prompt:</strong> ${prompt}</pre>` +
                `<pre><strong>Response:</strong> ${response}</pre>`;
            container.appendChild(divElement);
        });
    }
</script>

<form method="POST" on:submit|preventDefault={sendPrompt}>
    <h3>Prompt:</h3>
    <label for="prompt">
        <textarea name="prompt" rows="4" id="prompt" bind:value={prompt}
        ></textarea>
    </label>
    <button type="submit" on:keydown={sendPrompt}>Send</button>
</form>

{#each messages as message}
    <div>
        <!-- {#await messages_var then message} -->
        <pre>{message}</pre>
        <!-- {/await} -->
    </div>
{/each}
<button onclick="saveMessages()">Save Messages</button>

<div id="messages-container"></div>

<style>
    textarea {
        width: 60%;
    }
    #messages-container {
        width: 60%;
        margin-top: 20px;
    }
</style>
