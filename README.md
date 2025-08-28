const cards = /* your FormArray or array of FormGroups */;
let duplicate = false;

cards.forEach((card, i) => {
  const currentCusip = card.get('cusipNumber').value;
  const currentTradeType = card.get('tradeType').value; // Example additional field

  // Check all other cards except the current index
  const isDuplicate = cards.some((otherCard, j) => 
    j !== i &&
    otherCard.get('cusipNumber').value === currentCusip &&
    otherCard.get('tradeType').value === currentTradeType // or compare more fields
  );
  if (isDuplicate) {
    duplicate = true;
    // Optionally: set custom error or mark form control
  }
});