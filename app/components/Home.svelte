<script lang="js">
  import { action, ListPicker } from "@nativescript/core";
  import { Template } from "svelte-native/components";

  let todos = [];
  let textFieldValue = "";

  let completed = [];

  const listWithoutItem = (list, item) =>
    list.filter(listItem => listItem !== item);

  const listWithItem = (list, item) => [item, ...list];

  async function onItemTap(args) {
    let item = todos[args.index];
    console.log(`Item ${item.name} at index: ${args.index} was tapped`);

    let result = await action(
      "Was möchtest du mit der Aufgabe machen?",
      "Abbrechen",
      ["Erledigt", "Löschen", "Abbrechen"]
    );

    switch (result) {
      case "Erledigt":
        completed = listWithItem(completed, item);
        todos = listWithoutItem(todos, item);
        break;
      case "Löschen":
        todos = listWithoutItem(todos, item);
        break;
      case "Abbrechen" || undefined:
        break;
    }
  }

  async function onCompletedItemTap(args) {
    let item = completed[args.index];
    console.log(`Dones: Item ${item.name} at index: ${args.index} was tapped`);

    let result = await action(
      "Was möchtest du mit der Aufgabe machen?",
      "Abbrechen",
      ["Nicht erledigt", "Löschen", "Abbrechen"]
    );

    switch (result) {
      case "Nicht erledigt":
        todos = listWithItem(todos, item);
        completed = listWithoutItem(completed, item);
        break;
      case "Löschen":
        completed = listWithoutItem(completed, item);
        break;
      case "Abbrechen" || undefined:
        break;
    }
  }

  function onButtonTap() {
    if (textFieldValue === "") return;
    console.log("New task added: " + textFieldValue + ".");
    todos = [{ name: textFieldValue }, ...todos];
    textFieldValue = "";
  }
</script>

<page>
  <actionBar title="Meine Aufgaben" />

  <tabView>
    <tabViewItem title="To Do">
      <gridLayout columns="*,120" rows="70,*">
        <textField
          col="0"
          row="0"
          bind:text={textFieldValue}
          hint="Neue Aufgabe..."
          editable="true"
          on:returnPress={onButtonTap}
        />
        <button
          col="1"
          row="0"
          text="Hinzufügen"
          on:tap={onButtonTap}
          class="-primary"
        />

        <listView items={todos} on:itemTap={onItemTap} row="1" colSpan="2">
          <Template let:item>
            <label text={item.name} textWrap="true" />
          </Template>
        </listView>
      </gridLayout>
    </tabViewItem>

    <tabViewItem title="Erledigt">
      <listView
        items={completed}
        on:itemTap={onCompletedItemTap}
        row="1"
        colSpan="2"
      >
        <Template let:item>
          <label text={item.name} textWrap="true" class="item-completed" />
        </Template>
      </listView>
    </tabViewItem>
  </tabView>
</page>

<style>
  tabViewItem {
    background-color: grey;
  }

  textField {
    font-size: 16;
  }

  label {
    font-size: 16;
  }

  .item-completed {
    text-decoration: line-through;
    color: #717171;
  }
</style>
