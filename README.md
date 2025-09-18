import { FormControl, FormGroup } from '@angular/forms';

const makeCard = (action: ActionType) =>
  new FormGroup({
    actionToggle: new FormControl(action),
  });

it('should correctly filter buy and sell cards', () => {
  const cards = [makeCard(ActionType.Buy), makeCard(ActionType.Sell), makeCard(ActionType.Buy)];

  // make ngOnChanges() read this value
  setGetter(component, cards);

  component.ngOnChanges();

  expect(component.buyCards).toHaveLength(2);
  expect(component.sellCards).toHaveLength(1);
});

it('should set both arrays to empty if cardValues is an empty array', () => {
  setGetter(component, []);

  component.ngOnChanges();

  expect(component.buyCards).toEqual([]);
  expect(component.sellCards).toEqual([]);
});

it('should ignore unknown actionToggle values', () => {
  // you can mix in a fake that has getRawValue if you prefer
  const unknown = new FormGroup({ actionToggle: new FormControl('Hold' as any) });
  const buy     = makeCard(ActionType.Buy);

  setGetter(component, [unknown, buy]);

  component.ngOnChanges();

  expect(component.buyCards).toHaveLength(1);
  expect(component.sellCards).toHaveLength(0);
});

