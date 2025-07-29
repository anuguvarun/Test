  cards.forEach((g, i) => {
      if (i !== index) {
        g.get('search')?.updateValueAndValidity({ onlySelf: true });
      }
    });

    return null;