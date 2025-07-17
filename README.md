import { FormBuilder } from '@angular/forms';
import { MyComponent } from './my.component';

describe('onConfirmRemove', () => {
  let component: MyComponent;
  const fb = new FormBuilder();

  beforeEach(() => {
    component = new MyComponent();
    component.cards = [
      fb.group({ name: ['A'] }),
      fb.group({ name: ['B'] }),
    ];
    component.errors = { 1: 'Some error' };
    component.removals = { 1: true };
    component.removeCard = { emit: jest.fn() } as any;
    component.addCard = jest.fn();
    component.calculateAdjustedBalance = jest.fn();
  });

  it('should remove a card and emit event', () => {
    component.onConfirmRemove(1);
    expect(component.cards.length).toBe(1);
    expect(component.removeCard.emit).toHaveBeenCalledWith(1);
  });

  it('should call addCard if cards become empty', () => {
    component.cards = [fb.group({ name: ['Only'] })];
    component.onConfirmRemove(0);
    expect(component.addCard).toHaveBeenCalled();
  });
});