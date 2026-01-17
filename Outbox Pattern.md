- Typical flow:
	- API call updates DB and publishes event to kafka
	- while the DB update is done in an transaction in which the kafka publish might also be present, like so:
```ts
app.post(APIRoutes.CREATE_PAYMENT, async (req, res) => {

try {

const key = requireIdempotencyKey(req);


const result = await handleIdempotency(key, req.body, async () => {
const { amountMinor, currency, direction } = req.body;

const payment = await repo.create({
	id: uuidv4(),
	amountMinor,
	currency,
	direction: direction as PaymentDirection,
	state: PaymentState.CREATED,
});

const event = {
event_id: uuidv4(),
event_type: PaymentState.CREATED,
payment_id: payment.id,
amount_minor: payment.amountMinor,
currency: payment.currency,
direction: payment.direction,
timestamp: new Date().toISOString(),
};

await publishEvent(KafkaTopics.PAYMENTS_EVENTS, event);

return payment.toJSON();
});

res.json(result);
} catch (e: any) {
res.status(400).json({ error: e.message });
}
});  
```
- There's a serious flaw here

![[Screenshot 2026-01-17 at 8.51.04 PM.png]]
![[Pasted image 20260117205139.png]]
![[Pasted image 20260117205150.png]]

![[Pasted image 20260117205202.png]]
![[Pasted image 20260117205227.png]]![[Pasted image 20260117205258.png]]
