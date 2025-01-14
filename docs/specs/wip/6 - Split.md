The split operation is the most important component of the Cashu system. The wallet `Alice` can use it to redeem tokens (i.e. receive new ones in return) that she received from `Carol`, or she can split her own tokens to a target amount she needs to send to `Carol`, if she does not have the necessary amounts to compose the target amount in her wallet already.

The basic idea is that `Alice` sends `Bob` a set of  `Proof`'s and a set of `BlindedMessage`'s with an equal amount. Additionally, she specifies the `amount` at which she would like to have the split. 

## 6.1 - Split to send

To make this more clear, we make an example of a typical case of sending tokens from `Alice` to `Carol`:

`Alice` has 64 satoshis in her wallet, composed of two tokens, one worth 32 sats and another two worth 16 sats. She wants to send `Carol` 40 sats but does not have the necessary tokens to combine them to reach the exact target amount of 40 sats. `Alice` requests a split from the mint. For that, she sends the mint `Bob` her tokens (`Proofs`) worth `[32, 16, 16]` and asks for a split at amount 40. The mint will then return her new tokens with the amounts `[32, 8, 16, 8]`. Notice that the first two tokens can now be combined to 40 sats. The original tokens that `Alice` sent to `Bob` are now invalidated.

## 6.2 - Split to receive

Another case of how split can be useful becomes apparent if we follow up the example above where `Alice` split her tokens ready to be sent to `Carol`.  `Carol` can receive these tokens, which means to invalidate the tokens she receives and redeem them for new ones, using the same mechanism. Only if `Carol` redeems them for new tokens that only she can spend, `Alice` can't double-spend them anymore and this simple transaction can be considered settled. `Carol` requests a split of the tokens (`Proofs`) worth `[32, 8]` at the amount `40` (the total amount) to receive back new tokens with the same total amount.

## Example

**Request** of `Alice`:

```http
POST https://mint.host:3338/split
```

With the data being of the form `SplitRequest`:

```json
{
	"proofs": Proofs,
	"outputs": MintRequest,
	"amount": int
}
```

With curl:

```bash
curl -X POST https://mint.host:3338/split -d \
{
	"proofs": 
		[
			{
				"id": "DSAl9nvvyfva",
				"amount": 2,
				"secret": "S+tDfc1Lfsrb06zaRdVTed6Izg",
				"C": "0242b0fb43804d8ba9a64ceef249ad7a60f42c15fe6d4907238b05e857527832a3"
			},
			{
			...
			}
		],
	"outputs":{
		"blinded_messages": 
		[
			{
				"amount": 2, 
				"B_": "02634a2c2b34bec9e8a4aba4361f6bf202d7fa2365379b0840afe249a7a9d71239"
			},
			{
			...
			}
		]
	},
	"amount": 40
}
```

If successful, `Bob` will respond