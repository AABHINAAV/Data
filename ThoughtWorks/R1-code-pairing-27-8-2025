# REPOSITORY 
https://github.com/techops-recsys-lateral-hiring/developer-joyofenergy-java/tree/main


# REQUIREMENTS 
## Display Cost of Last Week's Usage
### Description  
As an electricity consumer I want to be able to view my usage cost of the last week so that I can monitor my spending

### Acceptance Criteria:
1. Given I have a smart meter ID with price plan attached to it and usage data stored, when I request the usage cost then I am shown the correct cost of last week's usage
2. Given I have a smart meter ID without a price plan attached to it and usage data stored, when I request the usage cost then an error message is displayed

### How to calculate usage cost
Unit of meter readings : kW (KiloWatt)
Unit of Time : Hour (h)
Unit of Energy Consumed : kW * Hour = kWh
Unit of Tariff : $ per kWh (ex 0.2 $ per kWh)

To calculate the usage cost for a duration (D) in which lets assume we have captured N electricity readings (er1,er2,er3....erN)"


# SOLUTION
### PricePlanComparatorController class
    @GetMapping("/lastWeekConsumption/{smartMeterId}")
    public ResponseEntity<BigDecimal> getLastWeekConsumption(@PathVariable("smartMeterId") String smartMeterId) {
        if (smartMeterId == null || smartMeterId.isEmpty()) {
            throw new RuntimeException("Please provide a valid meter id");
        }

        BigDecimal response = pricePlanService.getLastWeekConsumptionCostOfElectricity(smartMeterId);

        return ResponseEntity
                .status(HttpStatus.FOUND)
                .body(response);

    }

### PricePlanService
    final int DAYS = 7;
    private final Map<String, String> pricePlanAssociatedMeter;
    .......

    public PricePlanService(List<PricePlan> pricePlans, MeterReadingService meterReadingService, Map<String, String> pricePlanAssociatedMeter) {
        .......
        this.pricePlanAssociatedMeter = pricePlanAssociatedMeter;
    }

    public BigDecimal getLastWeekConsumptionCostOfElectricity(String smartMeterId) {
        Optional<List<ElectricityReading>> readings = meterReadingService.getReadings(smartMeterId);
        if (readings.isEmpty()) {
            throw new RuntimeException("Meter Reading is Invalid. Please provide a valid Meter Id!");
        }

        Instant daysBefore = Instant.now().minus(DAYS, ChronoUnit.DAYS);
        List<ElectricityReading> electricityReadings = readings.get()
                .stream()
                .filter(
                        reading -> reading.time().isAfter(daysBefore)
                )
                .collect(Collectors.toList());
        if(electricityReadings.isEmpty()) {
            return BigDecimal.ZERO;
        }

        if (!pricePlanAssociatedMeter.containsKey(smartMeterId)) {
            throw new RuntimeException("Meter is not associated with any Price Plan for now.");
        }
        String pricePlanId = pricePlanAssociatedMeter.get(smartMeterId);

        Optional<PricePlan> pp = pricePlans
                .stream()
                .filter(
                        (p) -> p.getPlanName().equalsIgnoreCase(pricePlanId)
                )
                .findFirst();
        if (!pp.isPresent()) {
            throw new RuntimeException("Associated Price Plan not available.");
        }

        PricePlan pricePlan = pp.get();


        final BigDecimal averageReadingInKw = calculateAverageReading(electricityReadings);
        final BigDecimal usageTimeInHours = calculateUsageTimeInHours(electricityReadings);
        final BigDecimal energyConsumedInKwH = averageReadingInKw.multiply(usageTimeInHours);
        final BigDecimal cost = energyConsumedInKwH.multiply(pricePlan.getUnitRate());
        return cost;
    }

### Further things asked to do:
1. to refactor this code (divide this code in small functions)
2. use better names for variables and methods
3. make finding of readings of last 7 days in seperate method with generic days readings calculator
4. write test cases
