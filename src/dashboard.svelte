<script>
    import { Appwrite } from "appwrite";
    import { Link } from 'svelte-routing';
    import { token } from './stores.js';

    // Init your Web SDK
    const appwrite = new Appwrite();

    appwrite
        .setEndpoint('http://localhost/v1') // Your Appwrite Endpoint
        .setProject('61bcba0ba8eac') // Your project ID
    ;

    let expenses = [];
	let update_index = -1;

    window.onload = listDocs();

    function listDocs(){
		let promise = appwrite.database.listDocuments('61d5caa80bd88');

		promise.then(function (response) {
			console.log(response);
			expenses = response.documents;
		}, function (error) {
			alert(error); // Failure
		});
	}

	function add_expense(){
		alert("Expense added");
		
		event.preventDefault();
		let date = document.getElementById("date").value;
		let expense = document.getElementById("expense-title").value;
		let price = document.getElementById("amount").value;
		let obj = {
			"date": date, 
			"expense": expense, 
			"price": price
		};
		let promise1 = appwrite.database.createDocument('61d5caa80bd88', 'unique()', obj);

		promise1.then(function (response) {
			expenses = [...expenses, response];
			console.log(response); // Success
		}, function (error) {
			console.log(error); // Failure
		});
		
		document.getElementById("form").reset();
	}

	function updateValues(i){
		console.log("Button clicked", expenses[i]);
		document.getElementById("date").value = expenses[i].date;
		document.getElementById("expense-title").value = expenses[i].expense;
		document.getElementById("amount").value = expenses[i].price;
		document.getElementById('update_btn').removeAttribute("hidden");
		document.getElementById('submit_btn').setAttribute("hidden", "hidden");
		update_index = i;
		console.log(update_index);
	}

	function update_expense(){
		
		let date = document.getElementById("date").value;
		let expense = document.getElementById("expense-title").value;
		let price = document.getElementById("amount").value;

		let obj = {
			"date": date, 
			"expense": expense, 
			"price": price
		};

		event.preventDefault();

		let promise = appwrite.database.updateDocument(expenses[update_index].$collection, expenses[update_index].$id, obj);

		promise.then(function (response) {
			expenses[update_index].date = date;
			expenses[update_index].expense = expense;
			expenses[update_index].price = price;
			
			console.log(response); // Success
		}, function (error) {
			console.log(error); // Failure
		});

		document.getElementById("form").reset();
		document.getElementById('submit_btn').removeAttribute("hidden");
		document.getElementById('update_btn').setAttribute("hidden", "hidden");
	}

	function deleteValues(i){
		console.log(expenses[i].$collection, expenses[i].$id);
		let promise = appwrite.database.deleteDocument(expenses[i].$collection, expenses[i].$id);

		promise.then(function (response) {
			expenses = expenses.filter(expense => expense.$id !== expenses[i].$id)
			console.log(response); // Success
		}, function (error) {
			console.log(error); // Failure
		});
	}


    const logout = () => {
        localStorage.clear();
        token.set(localStorage.getItem('token'));
    }
</script>

<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css" integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">

<main>
<div>
    <div>
        <form id="form">
            <input type="date" id="date">
            <input type="text" placeholder="Expense title" id="expense-title">
            <input type="number" min="1" step="any" placeholder="Amount" id="amount"/>
            <!-- <input type="file" class="custom-file-input" id="inputGroupFile04" aria-describedby="inputGroupFileAddon04">
            <label class="custom-file-label" for="inputGroupFile04">Upload Receipt</label> -->
            <input type="submit" id="submit_btn" value="Submit" on:click={add_expense}>
            <input type="submit" id="update_btn" value="Update" hidden on:click={update_expense}>
        </form>
    </div>
    
    <div class="container">
        <div class="row">
            <div class="col cell">
                <b>Date</b>
              </div>
              <div class="col cell">
                <b>Expense</b>
              </div>
              <div class="col cell">
                <b>Price</b>
            </div>
            <div class="col cell">
                <b>Update</b>
            </div>
            <div class="col cell">
                <b>Delete</b>
            </div>
            <div class="col cell">
                <b>View Receipt</b>
            </div>
        </div>
          
        {#each expenses as expense, i}
        <div class="row">
        
          <div class="col cell">
            {expense.date}
          </div>
          <div class="col cell">
            {expense.expense}
          </div>
          <div class="col cell">
            {expense.price}
          </div>
          <div class="col cell">
            <button on:click={() => updateValues(i)}>🖊️</button>
          </div>
          <div class="col cell">
            <button on:click={() => deleteValues(i)}>❌</button>
          </div>
          <div class="col cell">
            
          </div>
        </div>
        {/each}
      </div>

    <button class="btn btn-secondary" on:click={logout}>
        Log out
    </button>
</div>
</main>

<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css" integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">

<style>
    main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

    @media (min-width: 640px) {
		main {
			max-width: none;
		}
	}

    .container{
        margin-top: 15px;
    }

	.cell{
		border: gray;
		border-style: solid;
		width: auto;
	}

	button{
		margin: 10px;
	}
</style>