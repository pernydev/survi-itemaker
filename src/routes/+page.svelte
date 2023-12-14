<script>
	import 'carbon-components-svelte/css/g90.css';
	import { Row, Column, TextInput, Button, Dropdown, CodeSnippet } from 'carbon-components-svelte';
	import TrashCan from 'carbon-icons-svelte/lib/TrashCan.svelte';

	let id = '';
	let name = '';
	let lore = '';
	let rarity = 'COMMON';

	let abilities = [];

	let code = '';

	$: {
		code = `registerItem("${id}", ItemType().apply {
	name = "${name}"
	description = "${lore}".split("\\n")
	rarity = "${rarity}"`;

		/*
		mutableListOf(
                Ability().apply {
                    name = "Teleportaatio"
                    description = "Teleporttaa sinut 5-palikkaa eteenpäin"
                    manaCost = 10
                    cooldown = 1
                    trigger = Trigger.RIGHT_CLICK
                    handler = { player ->
                        tpability(player)
                    }
                }
            )
		*/

		if (abilities.length > 0) {
			code += '\n	abilities = mutableListOf(';
			abilities.forEach((ability, index) => {
				code += `Ability().apply {
		name = "${ability.name}"
		description = "${ability.description}"
		manaCost = ${ability.manaCost}
		cooldown = ${ability.cooldown}
		trigger = Trigger.${ability.trigger}
		handler = { player ->
			// Handler code here
		}
	}`;
				if (index !== abilities.length - 1) {
					code += ',';
				}
			});
		}

		code += '\n})';
	}
</script>

<h1>Crafteri</h1>
<Row>
	<Column>
		<TextInput
			labelText="Tavaran ID"
			placeholder="itemit:dirt_sword"
			helperText="Käytä oikeaa namespacea! (minecraft, itemit, palikat, jne.)"
			bind:value={id}
		/>
	</Column>
	<Column>
		<TextInput
			labelText="Tavaran Nimi"
			placeholder="Dirtti miekka"
			helperText="Voit käytää Endown formaattia"
			bind:value={name}
		/>
	</Column>
	<Column>
		<TextInput
			labelText="Tavaran Lore"
			placeholder="Maailman parhasta mullasta käsintehty __terävä__ miekka!"
			helperText="Voit käytää Endown formaattia, käytä \n uuden rivin luomiseen"
			bind:value={lore}
		/>
	</Column>
	<Column>
		<Dropdown
			titleText="Harvinaisuus"
			selectedId="0"
			items={[
				{ id: '0', text: 'COMMON' },
				{ id: '1', text: 'UNCOMMON' },
				{ id: '2', text: 'EPIC' },
				{ id: '3', text: 'LEGENDARY' },
				{ id: '4', text: 'MYTHIC' },
				{ id: '5', text: 'PRICELESS' }
			]}
			bind:value={rarity}
		/>
	</Column>
</Row>

<h2>Ominaisuudet</h2>
{#each abilities as ability}
	<Row>
		<Column>
			<TextInput
				labelText="Ominaisuuden nimi"
				placeholder="Salama"
				helperText="Voit käytää Endown formaattia"
			/>
		</Column>
		<Column>
			<TextInput
				labelText="Ominaisuuden energiahinta"
				placeholder="100"
				helperText="Käytä kokonaislukuja"
			/>
		</Column>
		<Column>
			<TextInput
				labelText="Ominaisuuden cooldown"
				placeholder="5"
				helperText="Käytä kokonaislukuja, sekunteina, 0 = ei cooldownia"
			/>
		</Column>
		<Column>
			<TextInput
				labelText="Ominaisuuden kuvaus"
				placeholder="Salama iskee maahan"
				helperText="Voit käytää Endown formaattia, käytä \n uuden rivin luomiseen"
			/>
		</Column>
		<Column>
			<Dropdown
				titleText="Ominaisuuden trigger"
				selectedId="0"
				items={[
					{ id: '0', text: 'RIGHT_CLICK' },
					{ id: '1', text: 'SHIFT_RIGHT_CLICK' },
					{ id: '2', text: 'LEFT_CLICK' },
					{ id: '3', text: 'SHIFT_LEFT_CLICK' },
					{ id: '4', text: 'HIT' },
					{ id: '5', text: 'SNEAK' }
				]}
			/>
		</Column>
	</Row>
	<Button
		on:click={() => (abilities = abilities.filter((a) => a !== ability))}
		kind="danger-tertiary"
		icon={TrashCan}
		iconDescription="Poista ominaisuus"
	/>
{/each}
<Button on:click={() => (abilities = [...abilities, {}])}>Lisää ominaisuus</Button>

<CodeSnippet {code} type="multi" />
