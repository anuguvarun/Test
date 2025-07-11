@Output() card = new EventEmitter<(index: number) => Promise<void>>();

async triggerCardEvent() {
  if (!this.card.observers.length) return;

  // Emit a function call and wait for the parent's work to finish
  const parentHandler = this.card.emit.bind(this.card) as (fn: (index: number) => Promise<void>) => void;

  await new Promise<void>((resolve) => {
    parentHandler(async (index: number) => {
      // You pass index, and parent will execute this
      await resolve(); // <- This resumes the next line in child after parent completes
    });

    // You can put any logic here that should run AFTER the parent is done
    // But better: move the logic into `continueAfterParent()` as below
  });

  this.continueAfterParent(); // runs after parent finishes
}

continueAfterParent() {
  console.log('✅ Child logic after parent finishes');
}