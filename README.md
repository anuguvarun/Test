import { FormControl, FormGroup } from '@angular/forms';

const makeCard = (action: ActionType) =>
  new FormGroup({
    actionToggle: new FormControl(action),
  });


it('should correctly filter buy and sell cards', () => {
  component.cardValues = [
    makeCard(ActionType.Buy),
    makeCard(ActionType.Sell),
    makeCard(ActionType.Buy),
  ];

  component.ngOnChanges();

  expect(component.buyCards).toHaveLength(2);
  expect(component.sellCards).toHaveLength(1);
});

it('should set both arrays to empty if cardValues is empty', () => {
  component.cardValues = [];
  component.ngOnChanges();

  expect(component.buyCards).toEqual([]);
  expect(component.sellCards).toEqual([]);
});

it('should ignore unknown actionToggle values', () => {
  // If you want to keep this one as a fake object instead of FormGroup,
  // give it getRawValue()
  component.cardValues = [
    { getRawValue: () => ({ actionToggle: 'Hold' }), value: { actionToggle: 'Hold' } } as any,
    makeCard(ActionType.Buy),
  ] as any;

  component.ngOnChanges();

  expect(component.buyCards).toHaveLength(1);
  expect(component.sellCards).toHaveLength(0);
});