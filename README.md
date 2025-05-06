FATAL ERROR: Reached heap limit Allocation failed - JavaScript heap out of memory
1: 0x10035d770 node::Abort) [/Users/xxxxx/.nvm/versions/node/v18.20.4/bin/node]
2: 0x10035d954 node::ModifyCodeGenerationFromStrings(v8::Local v8::Context>, v8::Local<v8::Value>, bool) [/Users/xxxxx/.nvm/versions/node/v18.20.4/bin/node]
3: 0x1004b41a8 v8::internal::V8::FatalProcessOutOfMemory(v8::internal:Isolate*,

if (actiontoggle === ActionType.Sell && toggleValue === UnitType.Shares) -{
const accounts = this.accountPositions.accountPositions ?? |1;
const matchedPositionSymbol = accounts [0]?. positions?. find(
(position) = position. symbol === this. symbol

if (matchedPositionSymbol.quantity = null) {
quantity setValidators ( [Validators.required, Validators.max(0)]);
else {
quantity.setValidators([
Validators. required,
Validators.max(matchedPositionSymbol.quantity),

ngOnInit(): void {
this.subscription.add(
this card-get('actionToggle') .valueChanges.subscribe(() => {
this. updateTradeCount ();
})
) ;
this subscription.add (
this. card.get ('shareToggle'). valueChanges-subscribe(() => {
this.toggleClearValidate();
})
｝
ngOnDestroy(): void
{
this-subscription.unsubscribe();
