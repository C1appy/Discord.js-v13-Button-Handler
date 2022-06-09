To get started make a new file in your handler file or wherever you wish this to be.

**Paste this code below into said file**

```js
const { resolve } = require('path');
const { readdir } = require('fs').promises;
const chalk = require('chalk');
module.exports = (client) => {
  
  async function getFiles(dir) {
    const dirents = await readdir(dir, { withFileTypes: true });
    const files = await Promise.all(dirents.map((dirent) => {
      const res = resolve(dir, dirent.name);
      return dirent.isDirectory() ? getFiles(res) : res;
    }));
    return Array.prototype.concat(...files);
  }
  
  async function run(){
      const files = await getFiles("./Buttons-SelectMenus/Buttons/");
      const jsFiles = files.filter((f) => f.split(`.`).pop() === "js")
      if (jsFiles.length <= 0)
         return console.log(chalk.yellowBright.bold(`[Button-Handler] No loadable Buttons detected`));
          console.log(chalk.blueBright.bold(`[Button-Handler]: Loaded ${jsFiles.length} Buttons`))
      // console.log(chalk.magenta`Loading ${jsFiles.length} Buttons`);
      jsFiles.forEach((fileName, index) => {
          let props = require(fileName);
          // console.log(`[${`Loader`}] ${fileName} Buttons loaded (${index + 1})`);
          client.interactions.set(props.help.custom_id, props);
      });
  }
  run();

  client.on(`interactionCreate`, async (interaction) => {
    if (interaction.isButton()) {
      var cmd = client.interactions.get(interaction.customId);
      if (cmd) cmd.run(client, interaction);
    }
  });
};
```

After this is done go to your main file (normally index.js) and paste this somewhere

`require(./Handlers/Buttons)(client);`

**Make sure to change** `./Handlers/Buttons` **to the path where you have it**


Once done make a new folder in your main directory named `Buttons` and then make folders/files for each button
all you need to do is add your buttons customID into there and the code to be executed once triggered

**To use this**

```js

module.exports.run = async (client, interaction) => {

            //the code to be executed goes here

}
module.exports.help = {
    values: `your button ID here`,
};
```
