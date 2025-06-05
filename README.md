import { FormGroup, FormControl } from '@angular/forms';

it('should assign filtered cardValues to buyCards and sellCards', () => {
  const buyCard = new FormGroup({
    actionToggle: new FormControl('Buy')
  });

  const sellCard = new FormGroup({
    actionToggle: new FormControl('Sell')
  });

  component.cardValues = [buyCard, sellCard];

  component.ngOnInit();

  expect(component.buyCards?.length).toBe(1);
  expect(component.sellCards?.length).toBe(1);

  expect(component.buyCards[0].get('actionToggle')?.value).toBe('Buy');
  expect(component.sellCards[0].get('actionToggle')?.value).toBe('Sell');
});