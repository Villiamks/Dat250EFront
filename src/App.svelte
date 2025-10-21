<script>
    import NewUser from "./lib/NewUser.svelte";

    let userid = null;
    let user = null;
    let username = "";
    let nyBruker = false;
    let users = [];
    let polls = [];
    
    getAllUsers();
    getAllPolls();
    CheckLoggedinn();

    if ( userid !== "a" && userid !== null && userid !== "null"){
        getUserById(userid);
    }

    // From API:

    async function getAllUsers(){
        await fetch("http://localhost:8080/api/users")
        .then(res => {
            return res.json()
        })
        .then(data =>  {
            users = data;
        })
        .catch(error => console.log(error))
    }
    async function getUserById(id){
        await fetch("http://localhost:8080/api/users/find?id=" + id)
        .then(res => {
            return res.json();
        })
        .then(data => {
            user = data;
            username = data.username;
        })
        .catch(error => {
            localStorage.removeItem("userid");
        });
    }

    async function getAllPolls(){
        await fetch("http://localhost:8080/api/polls")
        .then(res => {
            return res.json();
        })
        .then(data =>  {
            polls = data;
            console.log(polls);
        })
        .catch(error => console.log(error))
    }

    async function createPoll(inData, opt){
        await fetch("http://localhost:8080/api/polls", {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(inData)
            })
            .then(response => {
                return response.json();
            })
            .then(data => {
                data;
                insertOptions({
                    id: data.id + "",
                    options: opt
                })
                location.reload();
            })
            .catch(error => {
                console.error('Error:', error);
            });
    }
    async function insertOptions(inData) {
        await fetch("http://localhost:8080/api/polls/insert", {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify(inData)
        })
        .then(res => res.json()).then(data => data).catch(e => console.log(e));
    }

    async function vote(inData) {
        await fetch("http://localhost:8080/api/votes", {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(inData)
            })
            .then(response => {
                return response.json();
            })
            .then(data => {
                data;
            })
            .catch(error => {
                console.error('Error:', error);
            });
    }

    //Login:

    function Login(){
        let name = document.getElementById("name").value;
        let password = document.getElementById("password").value;

        if (name === null || name === "" ){
            userid = "a";
        } else {
            for (let i = 0; i < users.length; i++){
                let tmp = users[i];
                if (tmp != null && tmp.username == name && tmp.password == password){
                    userid = tmp.id;
                }
            }
        }
        localStorage.setItem("userid", userid);
        location.reload();
    }

    function LoggOut(){
        localStorage.setItem("userid", "null");
        location.reload();
    }

    function CheckLoggedinn(){
        let stored = localStorage.getItem("userid");

        if (stored !== "null" || stored !== null){
            if ( stored === "a"){
                username = "Anon";
            } else {
                userid = stored;
            }
        }else {
            userid = "null";
        }
    }

    function toggleNyBruker(){
        nyBruker = !nyBruker;
    }

    //Polls:

    function RegisterPoll(){
        let question = document.getElementById("captioninp").value;
        let optionsinp = document.getElementsByClassName("optioninp");
        let opt = "";

        for (let i = 0; i < optionsinp.length; i++){
            let tmp = optionsinp[i].value;
            if (tmp !== null){
                opt += tmp + ",,";
            }
        }

        createPoll({
            question: question,
            userid: userid
        }, opt);
    }

    function AddPollOption(){
        let box = document.getElementById("newpollbox");
        box.insertAdjacentHTML("beforeend", "Option: <input type=Text class=optioninp /> <br>");
    }

    // Vote:

    function placeVote(option){
        vote({
            id: userid,
            voteoption: option.id
        })
        location.reload();
    }

</script>

<main>

    {#if nyBruker}
        <NewUser />
    {/if}

    {#if (userid !== "null") && (username !== "") }
        
        <h1> Hello {username}! 
            <button type="button" onclick={LoggOut} >Logg out</button>
        </h1>

        {#if (username !== "Anon")}
            <div class="box">
                <div>Caption: <input type="text" id="captioninp"/> </div>
                <div id="newpollbox">
                    Option: <input type="text" class="optioninp" /> <br>
                </div>
                <button type="button" onclick={AddPollOption} >Add Option</button>
                <button type="button" onclick={RegisterPoll} >Create Poll</button>
            </div>
        {/if}

        {#each polls as poll }
            <div class="box" >
                <div>Poll {poll.id}</div>
                <h2>{poll.question}</h2>

                {console.log(poll.options[0])}

                {#each poll.options as option }
                    <div>
                        {option.caption} 
                        <button type="button" onclick={() => placeVote(option)} > vote </button>
                    </div>
                {/each}
            </div>
        {/each}
    {/if}

    {#if ((userid === "null") || (username === "") ) && (!nyBruker) }
        <h1>Login</h1>

        <input type="text" id="name" placeholder="name"> <br>
        <input type="password" id="password" placeholder="password"> <br>

        <button type="button" onclick={Login} >Login</button>
        <button type="button" onclick={toggleNyBruker} >Registerer ny</button>
    {/if}
</main>
