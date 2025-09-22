describe('feesUnchecked', () => {
  let fixture: ComponentFixture<MyComponent>;
  let component: MyComponent;
  const modalServiceStub = { open: jest.fn() };

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [MyComponent],
      providers: [{ provide: ModalService, useValue: modalServiceStub }],
    }).compileComponents();

    fixture = TestBed.createComponent(MyComponent);
    component = fixture.componentInstance;

    component.feesIncluded = new FormControl(true);
    jest.spyOn(component.feesIncluded, 'setValue');  // so we can assert

    component.actionToggle = new FormControl(ActionType.Buy);
  });

  it('does nothing when feesIncluded.value is false', () => {
    component.feesIncluded.setValue(false);

    component.feesUnchecked();

    expect(modalServiceStub.open).not.toHaveBeenCalled();
  });

  it('opens modal and on resolve sets true', async () => {
    modalServiceStub.open.mockReturnValue({ result: Promise.resolve() });

    component.feesUnchecked();
    await Promise.resolve();

    expect(component.feesIncluded.setValue).toHaveBeenCalledWith(true);
  });

  it('on reject sets false', async () => {
    modalServiceStub.open.mockReturnValue({ result: Promise.reject('cancel') });

    component.feesUnchecked();
    await Promise.resolve();

    expect(component.feesIncluded.setValue).toHaveBeenCalledWith(false);
  });
});
