<script>
  import Section from './components/Section.svelte';
  import { createMachine, state, transition, interpret } from 'robot3';
  import { writable } from 'svelte/store';

  // constructor function to interpret the machine and wrap
  // the FSM service into a custom svelte store.
  // `event` will be used as initial context if passed
  export const useMachine = (machine, event = {}) => {
    // every time the state changes we will update our Svelte store
    const service = interpret(
      machine,
      (service) => {
        set({ state: service.machine.current, context: service.context });
      },
      event
    );

    const { subscribe, set } = writable({
      state: machine.current,
      // we want the service context and not machine context
      context: service.context,
    });

    return [{ subscribe }, service.send];
  };

  // create our Robot FSM definition
  const panelMachine = createMachine({
    closed: state(
      transition('showOne', 'one'),
      transition('showTwo', 'two'),
      transition('showThree', 'three')
    ),
    one: state(transition('close', 'closed')),
    two: state(transition('close', 'closed')),
    three: state(transition('close', 'closed')),
  });

  const [panelState, send] = useMachine(panelMachine);

  // close handler
  const close = () => send('close');

  // send in event to our FSM
  const toggle = (action, state) => {
    // if current state matches desired panel state, close it
    if ($panelState.state === state) {
      send('close');
      return;
    }
    // else first close, then open desired state
    send('close');
    send(action);
  };
</script>

<style>
  .wrapper {
    font-family: sans-serif;
  }
  .container {
    margin: 1em auto;
    max-width: 100ch;
  }
  .panel {
    background-color: #b9f0fe;
  }
  .buttons > button {
    padding: 0.5em 1em;
  }
</style>

<div class="wrapper">
  <div class="heading container">
    <h1>Svelte with Robot FSM Example</h1>
  </div>

  <div class="panel">
    {#if $panelState.state === 'one'}
      <Section title="One" {close} />
    {/if}

    {#if $panelState.state === 'two'}
      <Section title="Two" {close} />
    {/if}

    {#if $panelState.state === 'three'}
      <Section title="Three" {close} />
    {/if}
  </div>

  <div class="buttons container">
    <button on:click={() => toggle('showOne', 'one')}>One</button>
    <button on:click={() => toggle('showTwo', 'two')}>Two</button>
    <button on:click={() => toggle('showThree', 'three')}>Three</button>
  </div>
</div>
