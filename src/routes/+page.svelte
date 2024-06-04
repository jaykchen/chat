<script>
    import { onMount } from 'svelte';

    let messages = [];

    async function sendPrompt(event) {
        event.preventDefault();

        const promptElement = document.getElementById("prompt");

        if (!promptElement.value.trim()) return;

        const prompt = promptElement.value;

        const body = {
            model: "Codestral-22B-v0.1-hf-Q5_K_M",
            messages: [{ role: "user", content: prompt }],
        };

        try {
            const response = await fetch(
                "https://0x57fa64fb75d1b8c778063adcd81d99e525b6197d.gaianet.network/v1/chat/completions",
                {
                    method: "POST",
                    headers: {
                        "Content-Type": "application/json",
                        Authorization: `fake_token`,
                    },
                    body: JSON.stringify(body),
                }
            );

            if (response.ok) {
                const data = await response.json();
                const messageContent = data.choices[0].message.content;

                // Update messages array reactively
                messages = [...messages, { prompt, response: messageContent }];
                
                // Clear the input field
                promptElement.value = "";
            } else {
                console.error("Failed to fetch data:", response.statusText);
            }
        } catch (error) {
            console.error("Error:", error);
        }
    }

    function saveMessages() {
        let textToSave = "";

        messages.forEach(({ prompt, response }) => {
            textToSave += `Prompt:\n${prompt}\n\nResponse:\n${response}\n\n---\n\n`;
        });

       const blob=new Blob([textToSave],{type:"text/plain"});

       const linkElement=document.createElement("a");

       linkElement.download="prompts_and_responses.txt";

       linkElement.href=window.URL.createObjectURL(blob);

       document.body.appendChild(linkElement);

       linkElement.click();

      document.body.removeChild(linkElement);
        
  }

  onMount(() => {

      // Attach event listener to the form
      document.getElementById("promptForm").addEventListener("submit", sendPrompt);
  });
</script>

<form id="promptForm">
  <h3>Prompt:</h3>
  <label for="prompt">
      <textarea name="prompt" rows="4" id="prompt"></textarea>
  </label>
  <button type="submit">Send</button>
</form>

{#each messages as { prompt, response }}
<div class="message">
   <pre><strong>Prompt:</strong> {prompt}</pre>
   <pre><strong>Response:</strong> {response}</pre>
</div>
{/each}

<button on:click={saveMessages}>Save Prompts</button>

<style>
textarea{
   width :60%;
}
.message{
   width :60%;
   margin-top :20px;
}
</style>