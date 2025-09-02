it('sets Dollars when MF + Buy (covers line 213)', () => {
  const card = stubCard('X', SecurityType.MF);
  component.index = 0;
  component.cardFormGroup = card;

  // Ensure starting state
  card.get('shareToggle').setValue(null);

  // MF + Buy → hits: if (action.value === ActionType.Buy) { … }  // L213
  card.get('actionToggle').setValue(ActionType.Buy);
  component.onActionChange();

  expect(card.get('shareToggle').value).toEqual(UnitType.Dollars);
});

it('switches to FI, marks isFi, and sets Bonds (covers 220–221)', () => {
  const card = stubCard('X', SecurityType.MF); // start anywhere, then flip to FI
  component.index = 0;
  component.cardFormGroup = card;

  // Spy to prove we executed the FI branch body
  const updateEstimatedValueSpy = jest.spyOn(component, 'updateEstimatedValue');

  // FI case → hits: this.isFi = true; units.setValue(UnitType.Bonds);  // L220–221
  card.get('tradeType').setValue(SecurityType.FI);
  component.onActionChange();

  expect(component.isFi).toBe(true);                                  // proves L220 ran
  expect(card.get('shareToggle').value).toEqual(UnitType.Bonds);       // proves L221 ran
  expect(updateEstimatedValueSpy).toHaveBeenCalled();                  // inside FI block
});