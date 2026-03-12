import { Component, Input } from '@angular/core';
import { FormControl, FormGroup } from '@angular/forms';

@Component({
  selector: 'app-approval-stable-component',
  templateUrl: './approval-stable-component.html',
})
export class ApprovalStableComponent {
  tradeData!: TradeOrderAdminResponse;
  noteForm = new FormGroup({});
  showErrors = false;

  @Input() set tradeDetails(response: TradeOrderAdminResponse | null) {
    if (!response) {
      this.noteForm = new FormGroup({});
      return;
    }

    this.tradeData = response;
    this.noteForm = this.buildNoteForm(response);
  }

  private buildNoteForm(response: TradeOrderAdminResponse): FormGroup {
    const form = new FormGroup({});

    for (const order of response.items ?? []) {
      for (const item of order.tradeOrderItems ?? []) {
        form.addControl(
          this.getNoteControlName(order.tradeOrderRequestId, item.tradeOrderItemId),
          new FormControl({ value: null, disabled: true })
        );
      }
    }

    return form;
  }

  private getNoteControlName(
    tradeOrderRequestId: string,
    tradeOrderItemId: string
  ): string {
    return `Note-${tradeOrderRequestId}-${tradeOrderItemId}`;
  }

  getItemNoteControl(
    tradeOrderRequestId: string,
    tradeOrderItemId: string
  ): FormControl | null {
    return this.noteForm.get(
      this.getNoteControlName(tradeOrderRequestId, tradeOrderItemId)
    ) as FormControl | null;
  }

  editNote(tradeOrderRequestId: string, tradeOrderItemId: string): void {
    this.getItemNoteControl(tradeOrderRequestId, tradeOrderItemId)?.enable();
  }

  saveNote(tradeOrderRequestId: string, tradeOrderItemId: string): void {
    this.getItemNoteControl(tradeOrderRequestId, tradeOrderItemId)?.disable();
  }
}