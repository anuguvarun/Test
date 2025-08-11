describe('onAddTrade', () => {
  afterEach(() => jest.restoreAllMocks());

  it('adds one card and emits tradeAdded', () => {
    // Arrange
    const addCardSpy = jest.spyOn(component, 'addCard');
    const emitSpy = jest.spyOn(component.tradeAdded, 'emit');
    const initialCards = component.cards.length;
    const initialRemovals = component.removals.length;

    // If addCard() calls presenter.createForm(), verify that too:
    const mockForm = new FormGroup({});
    const createFormSpy = jest
      .spyOn(presenter, 'createForm')
      .mockReturnValue(mockForm);

    // Act
    component.onAddTrade();

    // Assert
    expect(addCardSpy).toHaveBeenCalledTimes(1);
    expect(createFormSpy).toHaveBeenCalledTimes(1); // only if relevant
    expect(component.cards).toHaveLength(initialCards + 1);
    expect(component.cards[component.cards.length - 1].form)
      .toBe(mockForm); // optional but robust
    expect(component.removals).toHaveLength(initialRemovals);
    expect(emitSpy).toHaveBeenCalledTimes(1); // consider toHaveBeenCalledWith(payload)
  });
});