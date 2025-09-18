import { FormControl, FormGroup } from '@angular/forms';
// import { ActionType } from '...';  // make sure this enum is imported

// small helper so the forms look like the real thing in your component
const mkCard = (o: {
  tradeType?: string;
  actionToggle?: any;   // ActionType | string
  shareToggle?: string;
} = {}) =>
  new FormGroup({
    tradeType:   new FormControl(o.tradeType   ?? 'ETF'),
    actionToggle:new FormControl(o.actionToggle?? ActionType.Buy),
    shareToggle: new FormControl(o.shareToggle ?? 'Dollars'),
  });

it('should ignore unknown actionToggle values', () => {
  const hold = mkCard({ actionToggle: 'Hold' });          // unknown value
  const buy  = mkCard({ actionToggle: ActionType.Buy });  // valid value

  component.cardValues = [hold, buy] as any;

  expect(component.buyCards).toHaveLength(1);
  expect(component.sellCards).toHaveLength(0);
});

it('should analytics track Event', () => {
  const form1 = mkCard({ tradeType: 'ETF',  actionToggle: 'Buy',    shareToggle: 'Dollars' });
  const form2 = mkCard({ tradeType: 'MF',   actionToggle: 'Sell',   shareToggle: 'Shares'  });

  component.cardValues = [form1, form2];

  // one event with both cards in the payload
  expect(analyticsServiceStub.trackEvent).toHaveBeenCalledTimes(1);

  const eventArg = (analyticsServiceStub.trackEvent as jest.Mock).mock.calls[0][0];
  expect(eventArg).toBeInstanceOf(PurchaseEvent);
  expect(eventArg).toEqual(
    expect.objectContaining({
      type: 'DonorFlex Trade Card',
      products: expect.arrayContaining([
        expect.objectContaining({
          // adapt these to whatever mapTradeCardToAnalytics outputs:
          name1: 'Trade card 1',
          data31: 'Sell',
          data44: 'Shares',
        }),
        expect.objectContaining({
          name1: 'Trade card 1',
          data31: 'Buy',
          data44: 'Dollars',
        }),
      ]),
    })
  );
});