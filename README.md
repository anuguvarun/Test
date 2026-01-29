try {
            TradeOrderSummaryDTO response =
                    tradeOrderAdminService.getTradeOrderSummary();
            return new ResponseEntity<>(response, HttpStatus.OK);
        } catch (Exception ex) {
            log.error("Error while fetching trade order summary", ex);
            return new ResponseEntity<>(HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }

---
public TradeOrderSummaryDTO getTradeOrderSummary() {

        TradeOrderSummaryDTO dto = new TradeOrderSummaryDTO();

        Integer needsActionCount = fetchNeedsActionCount();
        Integer orderFlowCount = fetchOrderFlowManagementCount();

        dto.setNeedsActionCount(needsActionCount);
        dto.setOrderFlowManagementCount(orderFlowCount);

        ZonedDateTime now = ZonedDateTime.now();
        dto.setSystemDate(now.toLocalDate().toString());
        dto.setSystemTime(now.toLocalTime().toString());
        dto.setTimeZone(now.getZone().getId());

        return dto;
    }

    private Integer fetchNeedsActionCount() {
        // TODO: replace with repository / DB call
        return 5;
    }

    private Integer fetchOrderFlowManagementCount() {
        // TODO: replace with repository / DB call
        return 8;
    }