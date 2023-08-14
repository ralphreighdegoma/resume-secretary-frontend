<script>
    let input = "";
    let loading = false;
    let responseMessage = "";
    let showAlert = false;
    let conversationContainer; // Reference to the conversation container element
    let conversations = [
        {
            message: "Hello, I'm Ralph's A.I Secretary. How can I help you?",
            isAi: true,
            datetime: new Date()
        }
    ];

    function handleKeyDown(event) {
        if (event.key === 'Enter') {
            document.querySelector(".typing");

            event.preventDefault(); // Prevent the default behavior (form submission)
            handleSubmit();
        }
    }

    const handleSubmit = () => {

        //scrolldown to bottom
        conversationContainer.scrollTop = conversationContainer.scrollHeight; // Scroll to the bottom


        loading = true;
        //save conversation
        conversations.push({
            message: input,
            isAi: false,
            datetime: new Date()
        });

        conversations = conversations


        fetch(`http://localhost:8082/api/message?input=${input}`)
        .then(response => response.json())
        .then(data => {

            //scrolldown to bottom
            document.querySelector(".typing");


            responseMessage = data.message.replace(/\n/g, "<br>");
            showAlert = true;

            //save conversation
            conversations.push({
                message: responseMessage,
                isAi: true,
                datetime: new Date()
            });

            conversations = conversations

        })
        .catch(error => console.error(error))
        .finally(() => {
            loading = false;
        });

        input = "";


    };

</script>


<head>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">
</head>


<style>
  .center-content {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }

  /* Adjust styles for mobile devices */
  @media (max-width: 576px) {
    .center-content {
      height: auto;
      padding: 20px;
    }
  }

  #inputField {
    border-radius: 0px !important;
    position: relative;
    z-index: 1;
    margin-bottom: 3px !important;
  }
  #submitButton {
    width: 100%;
    border-radius: 0px !important;
  }
  .alert-info {
    border-radius: 0px !important;
    text-align: right;
  }
  .alert-success {
    border-radius: 0px !important;
  }
  .chat-wrapper {
    background: #f6f6f6;
    max-width: 600px;
    padding: 8px;
    height: 100vh;
  }

  .conversation {
    height: 85%;
    overflow-y: scroll;
  }

  .action-wrapper {
    position: absolute;
    bottom: 0px;
    width: 100%;
    margin-bottom: 24px;
    padding: 17px;
    background: white;

  }
</style>


<div class="center-content">
  <div class="row">
    <div class=" justify-content-center chat-wrapper">
      <div class="col-md-12 conversation" bind:this={conversationContainer}>
        {#if (conversations).length > 0}
            {#each conversations as conversation}
                {#if !conversation.isAi}
                    <div class="alert alert-info" role="alert">
                        <strong>You:</strong> {conversation.message}
                        <br/>
                        <span class="badge bg-primary">{conversation.datetime.toLocaleString()}</span>
                    </div>
                {/if}

                {#if conversation.isAi}
                    <div class="alert alert-success" role="alert">
                        <strong>Ralph:</strong> {@html conversation.message}
                        <br/>
                        <span class="badge bg-primary">{conversation.datetime.toLocaleString()}</span>
                    </div>
                {/if}
            {/each}
        {/if}

        {#if loading}
            <i class="fa fa-spinner typing fa-spin"></i> Typing...
        {/if}
      </div>
      <div class="col-md-12">
        <div class="mt-2">
            <input on:keydown={handleKeyDown}  bind:value={input} id="inputField" type="text" class="form-control" placeholder="Ask Ralph's A.I Secretary">
            <button id="submitButton" on:click={handleSubmit}  class="btn btn-primary btn-block" disabled={loading || input == ""}>
                {#if loading }
                <span>
                    <i class="fa fa-spinner fa-spin"></i> Loading...
                </span>
                {:else}
                <span>Submit</span>
                {/if}
            </button>
        </div>
      </div>
    </div>
  </div>
</div>