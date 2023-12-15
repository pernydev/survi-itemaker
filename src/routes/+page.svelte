<script>
	import 'carbon-components-svelte/css/g90.css';
	import { Row, Column, TextInput, Button, Dropdown, CodeSnippet } from 'carbon-components-svelte';
	import TrashCan from 'carbon-icons-svelte/lib/TrashCan.svelte';

	let id = '';
	let name = '';
	let lore = '';
	let rarity = 'COMMON';

	let abilities = [];

	let stats = {
		baseDamage: 0,
		baseArmor: 0,
		baseHealth: 0,
		baseSpeed: 0,
		baseMana: 0,
	};

	let code = '';

	$: {
		code = `registerItem("${id}", ItemType().apply {
	name = "${name}"
	description = "${lore}".split("\\n")
	rarity = "${rarity}"`

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
		if (stats.baseDamage > 0) {
			code += `\n	baseDamage = ${stats.baseDamage}.0f`;
		}
		if (stats.baseArmor > 0) {
			code += `\n	baseArmor = ${stats.baseArmor}.0f`;
		}
		if (stats.baseHealth > 0) {
			code += `\n	baseHealth = ${stats.baseHealth}.0f`;
		}
		if (stats.baseSpeed > 0) {
			code += `\n	baseSpeed = ${stats.baseSpeed}.0f`;
		}
		if (stats.baseMana > 0) {
			code += `\n	baseMana = ${stats.baseMana}.0f`;
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
			items={[
				{ id: '0', text: 'COMMON' },
				{ id: '1', text: 'UNCOMMON' },
				{ id: '2', text: 'EPIC' },
				{ id: '3', text: 'LEGENDARY' },
				{ id: '4', text: 'MYTHIC' },
				{ id: '5', text: 'PRICELESS' }
			]}
			bind:selectedId={rarity}
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
				bind:value={ability.name}
			/>
		</Column>
		<Column>
			<TextInput
				labelText="Ominaisuuden energiahinta"
				placeholder="100"
				helperText="Käytä kokonaislukuja"
				bind:value={ability.manaCost}
			/>
		</Column>
		<Column>
			<TextInput
				labelText="Ominaisuuden cooldown"
				placeholder="5"
				helperText="Käytä kokonaislukuja, sekunteina, 0 = ei cooldownia"
				bind:value={ability.cooldown}
			/>
		</Column>
		<Column>
			<TextInput
				labelText="Ominaisuuden kuvaus"
				placeholder="Salama iskee maahan"
				helperText="Voit käytää Endown formaattia, käytä \n uuden rivin luomiseen"
				bind:value={ability.description}
			/>
		</Column>
		<Column>
			<Dropdown
				titleText="Ominaisuuden trigger"
				items={[
					{ id: '0', text: 'RIGHT_CLICK' },
					{ id: '1', text: 'SHIFT_RIGHT_CLICK' },
					{ id: '2', text: 'LEFT_CLICK' },
					{ id: '3', text: 'SHIFT_LEFT_CLICK' },
					{ id: '4', text: 'HIT' },
					{ id: '5', text: 'SNEAK' }
				]}
				bind:selectedId={ability.trigger}
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

<Row>
	<Column>
		<TextInput
			labelText="Vahinko"
			placeholder="10"
			helperText="Käytä kokonaislukuja"
			bind:value={stats.baseDamage}
		/>
	</Column>
	<Column>
		<TextInput
			labelText="Armor"
			placeholder="10"
			helperText="Käytä kokonaislukuja"
			bind:value={stats.baseArmor}
		/>
	</Column>
	<Column>
		<TextInput
			labelText="Health"
			placeholder="10"
			helperText="Käytä kokonaislukuja"
			bind:value={stats.baseHealth}
		/>
	</Column>
	<Column>
		<TextInput
			labelText="Speed"
			placeholder="10"
			helperText="Käytä kokonaislukuja"
			bind:value={stats.baseSpeed}
		/>
	</Column>
	<Column>
		<TextInput
			labelText="Mana"
			placeholder="10"
			helperText="Käytä kokonaislukuja"
			bind:value={stats.baseMana}
		/>
	</Column>
</Row>

<Button on:click={() => (abilities = [...abilities, {}])}>Lisää ominaisuus</Button>

<h2>Statsei</h2>

<CodeSnippet {code} type="multi" />
