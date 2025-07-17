it('should remove card and related data at given index', () => {
  const component = new YourComponent();
  component.cards = ['card1', 'card2', 'card3'];
  component.errors = { 1: 'error1' };
  component.removals = { 1: 'remove1' };
  component.removeCard = { emit: jest.fn() };
  component.addCard = jest.fn();
  component.calculateAdjustedBalance = jest.fn();

  component.onConfirmRemove(1);

  expect(component.cards).toEqual(['card1', 'card3']);
  expect(component.errors[1]).toBeUndefined();
  expect(component.removals[1]).toBeUndefined();
  expect(component.removeCard.emit).toHaveBeenCalledWith(1);
  expect(component.calculateAdjustedBalance).toHaveBeenCalled();
});

it('should call addCard if all cards are removed', () => {
  const component = new YourComponent();
  component.cards = ['card1'];
  component.errors = {};
  component.removals = {};
  component.removeCard = { emit: jest.fn() };
  component.addCard = jest.fn();
  component.calculateAdjustedBalance = jest.fn();

  component.onConfirmRemove(0);

  expect(component.cards.length).toBe(0); // temporarily empty before addCard
  expect(component.addCard).toHaveBeenCalled();
});