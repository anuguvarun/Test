it('calls addCard when all cards are removed', () => {
    const form1 = new FormGroup({
      actionToggle: new FormControl('Buy'),
      estimatedValue: new FormControl('123'),
      shareToggle: new FormControl('true'),
    });

    component.cards = [form1]; // only one card
    const addCardSpy = jest.spyOn(component, 'addCard');

    component.onConfirmRemove(0);

    expect(addCardSpy).toHaveBeenCalled();
  });
});