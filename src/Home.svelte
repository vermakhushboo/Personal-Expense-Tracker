<script>
    import { Appwrite } from "appwrite";
    import { navigate } from 'svelte-routing';
    import { token } from './stores.js';

    let email, password, login_email, login_password;
    $: isAuthenticated = $token;

    // Init your Web SDK
    const appwrite = new Appwrite();

    appwrite
        .setEndpoint('http://localhost/v1') // Your Appwrite Endpoint
        .setProject('61bcba0ba8eac') // Your project ID
    ;

    const login = () => {
        localStorage.setItem('token', '1');
        token.set(localStorage.getItem('token'));

        let promise = appwrite.account.createSession(login_email, login_password);

		promise.then(function (response) {
			navigate('/dashboard', { replace: true });
		}, function (error) {
			alert(error);
		});
    };

    const signup = () => {
        localStorage.setItem('token', '1');
        token.set(localStorage.getItem('token'));

        let promise = appwrite.account.create('unique()', email, password);

		promise.then(function (response) {
			navigate('/dashboard', { replace: true });
		}, function (error) {
			alert(error);
		});
    };
</script>

<main>
    <h1>Personal Expense Tracker</h1>
    <div class="container signupcontainer">
        <span id="signup">
            <div>
                <input bind:value={email}>
            </div>
            <div>
                <input bind:value={password} type="password">
            </div>
            <div>
                <button on:click={signup}>
                    Sign up
                </button>
            </div>
        </span>
        <span id="login">
            <div>
                <input bind:value={login_email}>
            </div>
            <div>
                <input bind:value={login_password} type="password">
            </div>
            <div>
                <button on:click={login}>
                    Log In
                </button>
            </div>
        </span>
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

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}

	.signupcontainer{
		background-color: rgb(253, 211, 211);
		width: 60%;
		margin: auto;
		padding: 10px;
	}

	button{
		margin: 10px;
	}
</style>