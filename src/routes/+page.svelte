<script>
	import { onMount } from 'svelte';
  
	let candidates = [];
	let selectedCandidate = null;
	let isModalOpen = false;
	let isPopupOpen = false;
	let isAddModalOpen = false;
  
	let newCandidate = {
	  firstName: '',
	  surname: '',
	  mobile: '',
	  email: '',
	};
  
	async function fetchData() {
	  try {
		const apiKey = 'TEST1236C4CF23E6921C41429A6E1D546AC9535E';
		const apiUrl = `https://api.recruitly.io/api/candidate/?apiKey=${apiKey}`;
		const response = await fetch(apiUrl);
		const apiData = await response.json();
		candidates = apiData.data;
		console.log(candidates);
	  } catch (error) {
		console.error('Error fetching data:', error);
	  }
	}
  
	async function openEditPopup(candidate) {
	  selectedCandidate = candidate;
	  isPopupOpen = true;
	}
  
	async function editCandidate(updatedCandidate) {
	  const index = candidates.findIndex((candidate) => candidate.id === updatedCandidate.id);
	  if (index !== -1) {
		candidates[index] = updatedCandidate;
		const apiKey = 'TEST1236C4CF23E6921C41429A6E1D546AC9535E';
		const apiUrl = `https://api.recruitly.io/api/candidate/?apiKey=${apiKey}`;
		try {
		  const response = await fetch(apiUrl, {
			method: 'POST',
			headers: {
			  'Content-Type': 'application/json',
			},
			body: JSON.stringify(updatedCandidate),
		  });
  
		  if (response.ok) {
			console.log('Candidate updated successfully!');
		  } else {
			console.error('Failed to update candidate on the server');
		  }
		} catch (error) {
		  console.error('Failed to update candidate:', error);
		}
	  }
	  isPopupOpen = false;
	}
  
	async function deleteCandidate(candidate) {
	  const confirmation = confirm('Are you sure you want to delete this candidate?');
	  if (confirmation) {
		candidates = candidates.filter((c) => c.id !== candidate.id);
		const apiKey = 'TEST1236C4CF23E6921C41429A6E1D546AC9535E';
		const apiUrl = `https://api.recruitly.io/api/candidate/${candidate.id}/?apiKey=${apiKey}`;
		try {
		  const response = await fetch(apiUrl, {
			method: 'DELETE',
			headers: {
			  'Content-Type': 'application/json',
			},
		  });
  
		  if (response.ok) {
			console.log('Candidate deleted successfully!');
			alert('Deleted successfully!');
		  } else {
			console.error('Failed to delete candidate on the server');
		  }
		} catch (error) {
		  console.error('Failed to delete candidate:', error);
		}
	  }
	}
  
	async function addCandidate() {
	  const apiKey = 'TEST1236C4CF23E6921C41429A6E1D546AC9535E';
	  const apiUrl = `https://api.recruitly.io/api/candidate/?apiKey=${apiKey}`;
	  try {
		const response = await fetch(apiUrl, {
		  method: 'POST',
		  headers: {
			'Content-Type': 'application/json',
		  },
		  body: JSON.stringify(newCandidate),
		});
  
		if (response.ok) {
		  console.log('Candidate added successfully!');
		  candidates.push(newCandidate);
		  isAddModalOpen = false;
		} else {
		  console.error('Failed to add candidate on the server');
		}
	  } catch (error) {
		console.error('Failed to add candidate:', error);
	  }
	}
  
	onMount(fetchData);
  </script>
  <button on:click={() => { isAddModalOpen = true; resetNewCandidate(); }}>Add Candidate</button>
  <main>
	<table>
	  <thead>
		<tr>
		  <th>ID</th>
		  <th>First Name</th>
		  <th>Surname</th>
		  <th>Mobile</th>
		  <th>Email</th>
		  <th>Action</th>
		</tr>
	  </thead>
	  <tbody>
		{#each candidates as candidate (candidate.id)}
		  <tr>
			<td>{candidate.id}</td>
			<td>{candidate.firstName}</td>
			<td>{candidate.surname}</td>
			<td>{candidate.mobile}</td>
			<td>{candidate.email}</td>
			<td>
				<a href="#" on:click={() => openEditPopup(candidate)}>Edit</a>
     			<a href="#" on:click={() => deleteCandidate(candidate)}>Delete</a>
			</td>
		  </tr>
		{/each}
	  </tbody>
	</table>

  <!-- Add Popup -->
  {#if isAddModalOpen}
    <div class="popup-background">
      <div class="popup-content">
        <h2>Add Candidate</h2>
        <form>
          <label for="newFirstName">First Name:</label>
          <input type="text" id="newFirstName" bind:value={newCandidate.firstName} />

          <label for="newSurname">Surname:</label>
          <input type="text" id="newSurname" bind:value={newCandidate.surname} />

          <label for="newMobile">Mobile:</label>
          <input type="text" id="newMobile" bind:value={newCandidate.mobile} />

          <label for="newEmail">Email:</label>
          <input type="text" id="newEmail" bind:value={newCandidate.email} />

          <button on:click={addCandidate}>Add</button>
          <button on:click={() => { isAddModalOpen = false; resetNewCandidate(); }}>Cancel</button>
        </form>
      </div>
    </div>
  {/if}
	{#if isPopupOpen}
	<div class="popup-background">
	  <div class="popup-content">
		<h2>Edit Candidate</h2>
		<form>
		  <label for="firstName">First Name:</label>
		  <input type="text" id="firstName" bind:value={selectedCandidate.firstName} />
  
		  <label for="surname">Surname:</label>
		  <input type="text" id="surname" bind:value={selectedCandidate.surname} />
			
		  <label for="mobile">Mobile:</label>
		  <input type="text" id="mobile" bind:value={selectedCandidate.mobile} />
		  <!-- Add more form fields for mobile and email -->
		  <label for="email">Email:</label>
		  <input type="text" id="email" bind:value={selectedCandidate.email} />
		  <button on:click={() => editCandidate(selectedCandidate)}>Save</button>
		  <button on:click={() => isPopupOpen = false}>Cancel</button>
		</form>
	  </div>
	</div>
	{/if}

	
  </main>
  
  <style>
	/* Style for the main container */
	main {
	  font-family: Arial, sans-serif;
	  margin: 20px;
	}
  
	/* Style for the table */
	table {
	  width: 100%;
	  border-collapse: collapse;
	  margin-bottom: 20px;
	}
  
	th, td {
	  border: 1px solid #ccc;
	  padding: 8px;
	  text-align: left;
	}
  
	th {
	  background-color: #f2f2f2;
	}
  
	/* Style for buttons */
	button {
	  padding: 8px 12px;
	  margin-right: 8px;
	  cursor: pointer;
	  background-color: #007bff;
	  color: white;
	  border: none;
	  border-radius: 4px;
	}
  
	/* Style for popups */
	.popup-background {
	  background-color: rgba(0, 0, 0, 0.7);
	  position: fixed;
	  top: 0;
	  left: 0;
	  width: 100%;
	  height: 100%;
	  display: flex;
	  justify-content: center;
	  align-items: center;
	  z-index: 1000;
	}
  
	.popup-content {
	  background-color: white;
	  padding: 20px;
	  border-radius: 4px;
	  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.3);
	}
  
	/* Style for form inputs */
	label {
	  font-weight: bold;
	}
  
	input[type="text"] {
	  width: 100%;
	  padding: 8px;
	  margin-bottom: 10px;
	  border: 1px solid #ccc;
	  border-radius: 4px;
	}
  
	/* Style for buttons in popups */
	.popup-content button {
	  margin-top: 10px;
	  background-color: #007bff;
	  color: white;
	  border: none;
	  border-radius: 4px;
	  padding: 8px 12px;
	  cursor: pointer;
	}
  </style>
  
  