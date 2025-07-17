import { FormBuilder } from '@angular/forms';

it('should remove card and related data at given index', () => {
  const fb = new FormBuilder();
  component.cards = [
    fb.group({ name: ['card1'] }),
    fb.group({ name: ['card2'] }),
    fb.group({ name: ['card3'] }),
  ];
  component.errors = { 1: 'error1' };
  component.removals = { 1: 'remove1' };
  component.removeCard = { emit: jest.fn() };
  component.addCard = jest.fn();
  component.calculateAdjustedBalance = jest.fn();

  component.onConfirmRemove(1);

  expect(component.cards.length).toBe(2);
  expect(component.removeCard.emit).toHaveBeenCalledWith(1);
});