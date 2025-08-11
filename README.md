import { ComponentFixture, TestBed } from '@angular/core/testing';
import { ReactiveFormsModule, FormControl, FormGroup } from '@angular/forms';
import { EventEmitter } from '@angular/core';

// ⬇️ change to your real component + deps
import { RequestTradeComponent } from './request-trade.component';
class DummyDep {} // repeat for however many deps your ctor needs

describe('lines 205–231 coverage', () => {
  let fixture: ComponentFixture<RequestTradeComponent>;
  let component: RequestTradeComponent;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [RequestTradeComponent],
      imports: [ReactiveFormsModule],
      providers: [
        { provide: DummyDep, useValue: {} }, // add one entry per ctor dep
      ],
    }).compileComponents();

    fixture = TestBed.createComponent(RequestTradeComponent);
    component = fixture.componentInstance;
  });

  it('onConfirmRemove: removes card, deletes errors/removals, emits index, calls balance+refresh', () => {
    component.cards = [
      new FormGroup({ search: new FormControl('a') }),
      new FormGroup({ search: new FormControl('b') }),
      new FormGroup({ search: new FormControl('c') }),
    ] as any;

    component.errors = { 0: { x: 1 }, 1: { y: 2 }, 2: { z: 3 } } as any;
    component.removals = { 1: true } as any;

    component.removeCard = new EventEmitter<number>();
    const emitSpy = jest.spyOn(component.removeCard, 'emit');

    const addCardSpy = jest.spyOn(component as any, 'addCard'); // not called (still > 0 left)
    const calcSpy = jest.spyOn(component as any, 'calculateAdjustedBalance');
    const refreshSpy = jest.spyOn(component as any, 'refreshDuplicateValidation');

    component.onConfirmRemove(1);

    expect(component.cards.length).toBe(2);
    expect(component.cards[0].get('search')!.value).toBe('a');
    expect(component.cards[1].get('search')!.value).toBe('c');

    expect((component.errors as any)[1]).toBeUndefined();
    expect((component.removals as any)[1]).toBeUndefined();

    expect(emitSpy).toHaveBeenCalledWith(1);
    expect(addCardSpy).not.toHaveBeenCalled();
    expect(calcSpy).toHaveBeenCalled();
    expect(refreshSpy).toHaveBeenCalled();
  });

  it('onConfirmRemove: adds a card when the last one is removed', () => {
    component.cards = [new FormGroup({ search: new FormControl('only') })] as any;
    component.errors = { 0: { foo: true } } as any;
    component.removals = { 0: true } as any;
    component.removeCard = new EventEmitter<number>();

    const addCardSpy = jest.spyOn(component as any, 'addCard');
    const calcSpy = jest.spyOn(component as any, 'calculateAdjustedBalance');
    const refreshSpy = jest.spyOn(component as any, 'refreshDuplicateValidation');

    component.onConfirmRemove(0);

    expect(component.cards.length).toBe(0); // before addCard hook
    expect(addCardSpy).toHaveBeenCalled();
    expect(calcSpy).toHaveBeenCalled();
    expect(refreshSpy).toHaveBeenCalled();
  });

  it('onCancelRemove: clears removals[index]', () => {
    component.removals = { 2: true, 3: true } as any;
    component.onCancelRemove(2);
    expect((component.removals as any)[2]).toBeUndefined();
    expect((component.removals as any)[3]).toBe(true);
  });

  it('formatTime: returns h:mm with a.m./p.m.', () => {
    const am = component.formatTime('2025-01-01T09:05:00Z'); // any ISO string is fine
    const pm = component.formatTime('2025-01-01T17:30:00Z');

    expect(am).toMatch(/^\d{1,2}:\d{2} a\.m\.$/);
    expect(pm).toMatch(/^\d{1,2}:\d{2} p\.m\.$/);
  });

  it('refreshDuplicateValidation: updates only duplicate-error fields', () => {
    const g1 = new FormGroup({ search: new FormControl('a') });
    const g2 = new FormGroup({ search: new FormControl('b') });
    const g3 = new FormGroup({ search: new FormControl('c') });

    g1.get('search')!.setErrors({ duplicate: true });
    g2.get('search')!.setErrors({ duplicate: true, required: true });
    g3.get('search')!.setErrors({ required: true });

    const s1 = jest.spyOn(g1.get('search')!, 'updateValueAndValidity');
    const s2 = jest.spyOn(g2.get('search')!, 'updateValueAndValidity');
    const s3 = jest.spyOn(g3.get('search')!, 'updateValueAndValidity');

    (component as any).cards = { a: g1, b: g2, c: g3 };

    component.refreshDuplicateValidation();

    expect(s1).toHaveBeenCalledWith({ onlySelf: true, emitEvent: false });
    expect(s2).toHaveBeenCalledWith({ onlySelf: true, emitEvent: false });
    expect(s3).not.toHaveBeenCalled();
  });
});