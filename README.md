import { Component, DebugElement, ElementRef, QueryList } from '@angular/core';
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { Router } from '@angular/router';
import { FormGroup, FormControl, Validators } from '@angular/forms';
import { By } from '@angular/platform-browser';
import { RequestTradeComponent } from './request-trade.component'; // update path
import { RequestTradePresenter } from './request-trade.presenter'; // update path

@Component({
  template: `<app-request-trade></app-request-trade>` // update selector if needed
})
class TestHostComponent {}

describe('RequestTradeComponent (onNextStep)', () => {
  let fixture: ComponentFixture<TestHostComponent>;
  let debugEl: DebugElement;
  let component: RequestTradeComponent;

  const presenter = {
    createForm: jest.fn(),
    checkAccountNumberValidity: jest.fn(),
  };

  const routerStub = { navigateByUrl: jest.fn() };

  const makeQueryList = <T,>(items: T[]): QueryList<T> => {
    const ql = new QueryList<T>();
    ql.reset(items);
    return ql;
  };

  const buildCard = (valid: boolean): FormGroup =>
    new FormGroup({
      field: new FormControl(valid ? 'x' : '', Validators.required),
    });

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [TestHostComponent, RequestTradeComponent],
      providers: [
        { provide: Router, useValue: routerStub },
        { provide: RequestTradePresenter, useValue: presenter },
      ],
    }).compileComponents();
  });

  beforeEach(() => {
    fixture = TestBed.createComponent(TestHostComponent);
    debugEl = fixture.debugElement.query(By.directive(RequestTradeComponent));
    component = debugEl.componentInstance;

    component.headerForm = new FormGroup({
      brokerageAccount: new FormControl('', Validators.required),
    });

    window.scrollTo = jest.fn();
    (window as any).scrollY = 50;

    jest.useFakeTimers();
    jest.clearAllMocks();
  });

  it('calls presenter with multiple accounts and navigates when valid and balance >= 0', () => {
    component.accounts = [{}, {}];
    component.headerForm.setValue({ brokerageAccount: 'ACC' });
    component.cards = [buildCard(true), buildCard(true)];
    component.adjustedAvailableBalance = 5;

    component.onNextStep();

    expect(presenter.checkAccountNumberValidity).toHaveBeenCalled();
    expect(routerStub.navigateByUrl).toHaveBeenCalledWith('/invest/trade-order-confirm');
  });

  it('does not call presenter with single account', () => {
    component.accounts = [{}];
    component.headerForm.setValue({ brokerageAccount: 'ONLY' });
    component.cards = [buildCard(true)];
    component.adjustedAvailableBalance = 5;

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

  it('sets errors and scrolls to first invalid card element', () => {
    component.accounts = [{}, {}];
    component.headerForm.setValue({ brokerageAccount: 'ACC' });
    component.cards = [buildCard(false), buildCard(true)];

    const div = document.createElement('div');
    div.scrollIntoView = jest.fn();
    jest.spyOn(div, 'getBoundingClientRect').mockReturnValue(new DOMRect(0, 200, 0, 0));
    const elementRef = new ElementRef<HTMLElement>(div);

    component.stepCardElements = makeQueryList([elementRef]);

    component.onNextStep();
    jest.runOnlyPendingTimers();

    expect(div.scrollIntoView).toHaveBeenCalledWith({
      behavior: 'smooth',
      block: 'center',
      inline: 'nearest',
    });
    expect(window.scrollTo).toHaveBeenCalledWith({ top: 200 + 50 - 80, behavior: 'smooth' });
  });

  it('does not scroll if element is missing at index', () => {
    component.accounts = [{}, {}];
    component.headerForm.setValue({ brokerageAccount: 'ACC' });
    component.cards = [buildCard(false)];
    component.stepCardElements = makeQueryList<ElementRef<HTMLElement>>(
      [undefined as unknown as ElementRef<HTMLElement>]
    );

    component.onNextStep();
    jest.runOnlyPendingTimers();

    expect(window.scrollTo).not.toHaveBeenCalled();
  });
});