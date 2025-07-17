import { FormBuilder } from '@angular/forms';

describe('MyComponent', () => {
  let component: MyComponent;

  beforeEach(() => {
    component = new MyComponent();
    component.cards = [];
    component.errors = {};
    component.removals = {};
    component.removeCard = { emit: jest.fn() };
    component.addCard = jest.fn();
    component.calculateAdjustedBalance = jest.fn();
  });

  it('should remove a card and related data at given index', () => {
    const fb = new FormBuilder();

    component.cards = [
      fb.group({ name: ['card1'] }),
      fb.group({ name: ['card2'] }),
      fb.group({ name: ['card3'] }),
    ];

    component.errors = { 1: 'error1' };
    component.removals = { 1: 'remove1' };

    component.onConfirmRemove(1);

    expect(component.cards.length).toBe(2);
    expect(component.cards[0].value.name).toBe('card1');
    expect(component.cards[1].value.name).toBe('card3');
    expect(component.errors[1]).toBeUndefined();
    expect(component.removals[1]).toBeUndefined();
    expect(component.removeCard.emit).toHaveBeenCalledWith(1);
    expect(component.calculateAdjustedBalance).toHaveBeenCalled();
  });

  it('should call addCard if all cards are removed', () => {
    const fb = new FormBuilder();

    component.cards = [fb.group({ name: ['onlyCard'] })];
    component.errors = {};
    component.removals = {};

    component.onConfirmRemove(0);

    expect(component.cards.length).toBe(0); // before addCard is called
    expect(component.addCard).toHaveBeenCalled();
    expect(component.calculateAdjustedBalance).toHaveBeenCalled();
  });
});