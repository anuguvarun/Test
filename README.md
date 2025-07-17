import { FormBuilder } from '@angular/forms';

describe('onConfirmRemove', () => {
  let component: any;
  const fb = new FormBuilder();

  beforeEach(() => {
    component = {
      cards: [
        fb.group({ name: ['A'] }),
        fb.group({ name: ['B'] }),
        fb.group({ name: ['C'] }),
      ],
      errors: { 1: 'Some error' },
      removals: { 1: true },
      removeCard: { emit: jest.fn() },
      addCard: jest.fn(),
      calculateAdjustedBalance: jest.fn(),
    };
  });

  it('should remove the card and associated data, emit event, and recalculate balance', () => {
    component.onConfirmRemove = function (index: number) {
      this.cards = this.cards.slice(0, index).concat(this.cards.slice(index + 1));
      delete this.errors[index];
      delete this.removals[index];
      this.removeCard.emit(index);
      if (this.cards.length === 0) {
        this.addCard();
      }
      this.calculateAdjustedBalance();
    };

    component.onConfirmRemove(1);

    expect(component.cards.length).toBe(2);
    expect(component.cards[0].value.name).toBe('A');
    expect(component.cards[1].value.name).toBe('C');
    expect(component.errors[1]).toBeUndefined();
    expect(component.removals[1]).toBeUndefined();
    expect(component.removeCard.emit).toHaveBeenCalledWith(1);
    expect(component.calculateAdjustedBalance).toHaveBeenCalled();
    expect(component.addCard).not.toHaveBeenCalled();
  });

  it('should call addCard if all cards are removed', () => {
    component.cards = [fb.group({ name: ['Only'] })];
    component.errors = {};
    component.removals = {};

    component.onConfirmRemove = function (index: number) {
      this.cards = this.cards.slice(0, index).concat(this.cards.slice(index + 1));
      delete this.errors[index];
      delete this.removals[index];
      this.removeCard.emit(index);
      if (this.cards.length === 0) {
        this.addCard();
      }
      this.calculateAdjustedBalance();
    };

    component.onConfirmRemove(0);

    expect(component.cards.length).toBe(0);
    expect(component.addCard).toHaveBeenCalled();
    expect(component.calculateAdjustedBalance).toHaveBeenCalled();
  });
});