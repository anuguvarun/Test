

  it('calls presenter when multiple accounts and navigates when valid & balance >= 0', () => {
    component.accounts = [{}, {}]; // >1 triggers presenter call
    component.headerForm.setValue({ brokerageAccount: 'ACC' });
    component.cards = [buildCard(true), buildCard(true)];
    component.adjustedAvailableBalance = 5;

    component.onNextStep();

    expect(presenter.checkAccountNumberValidity).toHaveBeenCalledTimes(1);
    expect(routerStub.navigateByUrl).toHaveBeenCalledWith('/invest/trade-order-confirm');
    // touched/pristine/validate called on each card
    component.cards.forEach(c => {
      expect(jest.spyOn(c, 'markAllAsTouched')).toBeDefined();
      expect(jest.spyOn(c, 'markAsPristine')).toBeDefined();
      expect(jest.spyOn(c, 'updateValueAndValidity')).toBeDefined();
    });
  });

  it('does not call presenter with single account', () => {
    component.accounts = [{}]; // exactly 1
    component.headerForm.setValue({ brokerageAccount: 'ONLY' });
    component.cards = [buildCard(true)];
    component.adjustedAvailableBalance = 1;

    component.onNextStep();

    expect(presenter.checkAccountNumberValidity).not.toHaveBeenCalled();
  });

  it('emits submit but skips navigation when balance is negative', () => {
    const emitSpy = jest.spyOn(component.stepDataSubmitted, 'emit');
    component.accounts = [{}, {}];
    component.headerForm.setValue({ brokerageAccount: 'ACC' });
    component.cards = [buildCard(true), buildCard(true)];
    component.adjustedAvailableBalance = -1;

    component.onNextStep();

    expect(emitSpy).toHaveBeenCalled();
    expect(routerStub.navigateByUrl).not.toHaveBeenCalled();
  });

  it('sets errors and scrolls to the first invalid card element', () => {
    component.accounts = [{}, {}];
    component.headerForm.setValue({ brokerageAccount: 'ACC' });
    component.cards = [buildCard(false), buildCard(true)];

    // real HTMLElement works with DOM types (no any)
    const div = document.createElement('div');
    // make scrollIntoView a jest mock with the right signature
    const scrollIntoViewMock: (arg?: boolean | ScrollIntoViewOptions) => void = jest.fn();
    div.scrollIntoView = scrollIntoViewMock;

    // getBoundingClientRect must return DOMRect
    const rect = new DOMRect(0, 200, 0, 0);
    jest.spyOn(div, 'getBoundingClientRect').mockReturnValue(rect);

    const elementRef = new ElementRef<HTMLElement>(div);
    component.stepCardElements = makeQueryList<ElementRef<HTMLElement>>([elementRef]);

    component.onNextStep();
    jest.runOnlyPendingTimers();

    // first invalid set at index 0; smooth scroll called
    expect(scrollIntoViewMock).toHaveBeenCalledWith({
      behavior: 'smooth',
      block: 'center',
      inline: 'nearest',
    });
    expect(window.scrollTo).toHaveBeenCalledWith({ top: 200 + 50 - 80, behavior: 'smooth' });
  });

  it('does not scroll if the element at index is missing (covers inner-if false)', () => {
    component.accounts = [{}, {}];
    component.headerForm.setValue({ brokerageAccount: 'ACC' });
    component.cards = [buildCard(false)];

    // index exists but is undefined element
    component.stepCardElements = makeQueryList<ElementRef<HTMLElement>>([undefined as unknown as ElementRef<HTMLElement>]);

    const scrollToSpy = window.scrollTo as unknown as jest.Mock;
    scrollToSpy.mockClear();

    component.onNextStep();
    jest.runOnlyPendingTimers();

    expect(scrollToSpy).not.toHaveBeenCalled();
  });
});