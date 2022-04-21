<script lang="ts">
  import { onMount } from 'svelte'
  import { ethers } from 'ethers'
  import axios from 'axios'

  const ethereum: any | undefined = (window as any).ethereum
  let provider: any
  let signer: any

  const signMessage = `
🎉Popcateum X Klaymint🎉
Airdrop event to celebrate the partnership of Popcateum and KlayMint!

If you complete the signature successfully, we will airdrop 100 POP on the end date of the event.
`

  onMount(async () => {
    await signVerify()
  })

  async function addChain() {
    await ethereum.request({
      method: 'wallet_addEthereumChain',
      params: [
        {
          chainId: '0x4bd',
          chainName: 'Popcateum',
          nativeCurrency: {
            name: 'Popcat',
            symbol: 'POP',
            decimals: 18,
          },
          rpcUrls: ['https://dataseed.popcateum.org'],
          blockExplorerUrls: ['https://explorer.popcateum.org'],
        },
      ],
    })
  }

  async function requestAccount() {
    await ethereum.request({ method: 'eth_requestAccounts' })
  }

  async function getInfo() {
    provider = new ethers.providers.Web3Provider(ethereum)
    signer = provider.getSigner()
  }

  async function connect() {
    if (ethereum === undefined) {
      alert('There is no Metamask. Please install Metamask.')
      window.close()
      return
    }
    await getInfo()
    await addChain()
    await requestAccount()
  }

  async function signVerify() {
    await connect()
    const serverURL = 'http://localhost:3000/signing/sign'
    try {
      const signing = await signer.signMessage(signMessage)
      await axios.post(serverURL, {
        sig: signing,
      })
      alert('verify success')
    } catch (e) {
      if (e.code === 4001) {
        alert('Signature has been cancelled.')
      } else {
        alert('verify failed')
      }
    }
  }
</script>
