# suiwallet-py
blockchain sui official wallet python sdk,  suport use mnemonic seeds generate sui address and private key


## pip
```
pip install -r requirements.txt
```

## 
```python
from bip_utils import Bip39SeedGenerator, Bip44Coins, Bip44
from bech32 import bech32_encode, convertbits


# only support ed25519 schema
class Suiwallet:
    
    def __init__(self, mnemonic: str, password='') -> None:
        self.mnemonic: str = mnemonic.strip()
        self.password = password
        self.pk_prefix = 'suiprivkey'
        self.ed25519_schema = '00'
        
    def get_address_pk(self, pk_with_prefix=True):
        seed_bytes = Bip39SeedGenerator(self.mnemonic).Generate(self.password)
        bip44_mst_ctx = Bip44.FromSeed(seed_bytes, Bip44Coins.SUI).DeriveDefaultPath()
        address = bip44_mst_ctx.PublicKey().ToAddress()
        pk = bip44_mst_ctx.PrivateKey().Raw().ToHex() # hex type pk
        
        if pk_with_prefix: # sui official wallet cant import hex type pk to wallet
            pk_bytes_with_schema = bytes.fromhex(f'{self.ed25519_schema}{pk}')
            pk_bit_arr = convertbits(pk_bytes_with_schema, 8, 5)
            pk = bech32_encode(self.pk_prefix, pk_bit_arr) # result like "suiprivkey1q............"
            
        return address, pk
        
        

```

## test
```python
if __name__ == '__main__':
    mnc = 'life trap person three judge sadness curtain wise gas dynamic grow guard'
    sw = Suiwallet(mnc)
    add, pk = sw.get_address_pk()
    print(f'mnemonic seeds: {mnc}')
    print(f'address: {add}, private key: {pk}')
```
## result

```
mnemonic seeds: life trap person three judge sadness curtain wise gas dynamic grow guard
address: 0x974390383087ba689f0af383b2e9b2bea0b758f8ebe8a31ecbab21fb4cc98990, private key: suiprivkey1qpce53dxkltypj3y96nc9u6c096m27vj7p86yk4znv6j5rf04pf9uh20z7y
```

## import the mnemonic seed to fuel official wallet , get result

<img width="30%" alt="image" src="https://github.com/satisfywithmylife/suiwallet-py/assets/30144807/d9f684e6-d443-40ce-9977-8abb7f4448be">


## if you export private key from wallet, you will get same result

<img width="30%" alt="image" src="https://github.com/satisfywithmylife/suiwallet-py/assets/30144807/cd0cbd42-b747-4347-a49e-0c2ccb3b875d">


# last but important!
1. test the result and compare it with main web wallet app(such as: metamask, mathwallet, trustwallet...) before you deposit crypto assets to the address
2. some wallet may get diffrent result, because it may use diffrent derive path to generate wallet
3. learn about hd-wallet principle by your self
