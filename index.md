

---
pagetitle: A smart contract anyone can drain once people stop sending it money
command: pandoc index.md -o index.html -s
---

<style>
html {
      line-height: 1.5;
      font-family: Georgia, serif;
      font-size: 20px;
}
button, input[type=submit] {
  background-color: #04AA6D; /* Green */
  border: 1px solid black;
  color: white;
  padding: 15px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  cursor: pointer;
}
#amount {
  padding: 15px 15px 15px 15px;
  border-radius: 0;
  border: 1px solid black;
  font-size: 16px;
  margin-right: -1px;
}
img {max-width: min(500px, 100%); border: 1px solid black; margin: 0 auto; display: block;} hr, body {margin-top: 0; padding-top: 0}
body {padding-bottom: 15px;}
.humanchessgraphic {max-width: 400px; max-width: min(400px, 100%);}
<!-- .border {border: 1px solid black;} -->
h3 a, kbd a {text-decoration: none; color: inherit;}</style>

## A smart contract anyone can drain once people stop sending it money

The smart contract at `0xa5E51F10976Dc0C053048037b43FB0E24AC2D651` lets anyone drain its balance. If you have a Web3 wallet like [Metamask](https://metamask.io) installed, just click this button:

<button id=drain>Drain</button>

Before giving you its life savings, the smart contract first checks whether anyone sent Ethereum to it in the last 24 hours.

<form><input id=amount type="number" required value="0.001" min="0.001" step=any  onchange="this.value = this.value < 0.001 ? 0.001 : this.value"></input><input type=submit id=transfer value ="Send to contract"></form>

If no one did so (and why would they?), you'll receive its entire balance.

Recent transactions to the smart contract can be viewed on [Etherscan](https://etherscan.io/address/0xa5E51F10976Dc0C053048037b43FB0E24AC2D651){target="_blank"}. The source code, verified against the bytecode by Etherscan, can be found [here](https://etherscan.io/address/0xa5E51F10976Dc0C053048037b43FB0E24AC2D651#code){target="_blank"}.

<script src="https://cdn.jsdelivr.net/npm/web3@1.10.4/dist/web3.min.js"></script>
<script>
let abi = [{"inputs": [], "name": "drain", "outputs": [], "stateMutability": "nonpayable", "type": "function"}, {"inputs": [], "name": "lastTransaction", "outputs": [{"internalType": "uint256", "name": "", "type": "uint256"}], "stateMutability": "view", "type": "function"}, {"stateMutability": "payable", "type": "receive"}]
let token
let addr = '0xa5E51F10976Dc0C053048037b43FB0E24AC2D651'
let userAccount
let web3js

function poll () {
    // Get default account
    web3js.eth.getAccounts().then(function (accounts) {
        // Just keep updating, so the user's balance is updated after purchase
        userAccount = accounts[0]
    })
    token.methods.lastTransaction().then(function (ts) {
        // Just keep updating, so the user's balance is updated after purchase
        console.log(ts)
    })
    web3.eth.getBalance(addr).then(function (balance) {
        // Just keep updating, so the user's balance is updated after purchase
        console.log(balance)
    })
}

function startApp () {
        window.ethereum.enable();
        token = new web3js.eth.Contract(abi, addr)
        // Update account detail every 1 seconds
        setInterval(poll, 1000)
        $$('#drain').onclick = _ => token.methods.drain().send({from: userAccount});
        $$('form').onsubmit = _ => {
            web3js.eth.sendTransaction({
                from: userAccount,
                value: web3js.utils.toWei($$('#amount').value, 'ether'),
                to: addr,
                gas: 35000
          })
          return false
        }
        return false
}

var $$ = function (e) { return document.querySelector(e) }


window.onload = _ => {
if (window.ethereum) {
    web3js = new Web3(window.ethereum)
    $$('#drain').onclick = startApp
    $$('form').onsubmit = startApp
}
}
</script>
