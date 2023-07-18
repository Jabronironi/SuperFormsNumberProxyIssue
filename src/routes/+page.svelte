<script lang="ts">
	import { superForm, superValidateSync, stringProxy, numberProxy, dateProxy } from 'sveltekit-superforms/client';
	import { z } from 'zod';

	const schema = z.object({
		stringSample: z.string().min(1).max(10).optional(),
		numberSample: z.number().min(1).max(10).optional(),
		numberSample2: z.number().min(1).max(10).optional(),
		dateSample: z.date().min(new Date('1/1/2023')).max(new Date('1/1/2024')).optional()
	});

	const { errors, enhance, form, tainted } = superForm(superValidateSync(schema), {
		SPA: true,
		validators: schema,
		dataType: 'json',
		onSubmit({ cancel }) {
			if (!$tainted) cancel();
		},
		onUpdate({ form }) {
			if (form.valid) alert('valid');
            else alert('invalid');
		}
	});

	const stringSampleProxy = stringProxy(form, 'stringSample', { empty: 'undefined' });
    const numberSampleProxy = numberProxy(form, 'numberSample', { empty: 'undefined' });
    const numberSampleProxy2 = numberProxy(form, 'numberSample2', { empty: 'undefined' });
    const dateSampleProxy = dateProxy(form, 'dateSample', { format: 'datetime-local', empty: 'undefined' });
</script>

<svelte:head>
	<title>Super Forms Proxy Samples</title>
</svelte:head>
<h1>Superforms Proxy Samples</h1>
Note: All my tests were done in latest Edge.
<ul>
    <li>String sample works as expected. On blur, checks validation.  Clearing field and blurring clears validation error.</li>
    <li>Number sample (type number) does not work as expected.  On blur does NOT check validation.  On blur does not update $tainted store.  On submit shows valid when invalid. Does not update $tainted store.</li>
    <li>Number sample (type text) does work as expected, but you don't get the browser number control features.</li>
    <li>Date sample kind of works.  It updates $tainted on blur (great!).  It does not check validation on blur however.</li>
</ul>
<form method="POST" use:enhance>    
    <div>
        <label for="stringSample" style="font-weight: bold">String Sample (type string)</label>
        <div style="font-style: italic">Min length 1, Max length 10, optional</div>
        <input type="text" id="stringSample" bind:value={$stringSampleProxy} />
        {#if $errors.stringSample}<div style="color: red">{$errors.stringSample}</div>{/if}
    </div><br />
    <div>
        <label for="numberSample" style="font-weight: bold">Number Sample (type number)</label>
        <div style="font-style: italic">Min value 1, Max value 10, optional</div>
        <input type="number" id="numberSample" bind:value={$numberSampleProxy} />
        {#if $errors.numberSample}<div style="color: red">{$errors.numberSample}</div>{/if}
    </div><br />
    <div>
        <label for="numberSample2" style="font-weight: bold">Number Sample 2 (type text)</label>
        <div style="font-style: italic">Min value 1, Max value 10, optional</div>
        <input type="text" id="numberSample2" bind:value={$numberSampleProxy2} />
        {#if $errors.numberSample2}<div style="color: red">{$errors.numberSample2}</div>{/if}
    </div><br />
    <div>
        <label for="dateSample" style="font-weight: bold">Date Sample (type datetime-local)</label>
        <div style="font-style: italic">Min date 1/1/2023, Max date 1/1/2024, optional</div>
        <input type="datetime-local" id="dateSample" bind:value={$dateSampleProxy} />
        {#if $errors.dateSample}<div style="color: red">{$errors.dateSample}</div>{/if}
    </div><br />
    <div>
        <button type="submit" disabled={!$tainted}>Test</button>
    </div>    
</form>