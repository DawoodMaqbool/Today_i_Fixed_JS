
## Problem
We need to generate time slots of a given duration between a start and end time, while also excluding any unavailable time slots.

## Environment
- IDE Version: Visual Studio Code

## How you fix it
To solve the problem, I created a function called `generateTimeSlots` that takes in the necessary parameters for generating the time slots, and returns an array of `TimeSlot` objects. The function excludes any unavailable time slots provided in the `unavailableSlots` parameter.

## Solution
```typescript
interface TimeSlot {
  start: string;
  end: string;
}

interface UnavailableSlot {
  start: string;
  end: string;
}

/**
 * Generates time slots between given start and end time with a given duration, excluding the unavailable slots.
 *
 * @param startTime The start time for generating the time slots.
 * @param endTime The end time for generating the time slots.
 * @param duration The duration of each time slot in minutes.
 * @param unavailableSlots The unavailable time slots.
 *
 * @throws Error if start time is after end time, or duration is not a positive integer.
 *
 * @returns The generated time slots between the start and end time.
 */
export function generateTimeSlots(
  startTime: string,
  endTime: string,
  duration: number,
  unavailableSlots: UnavailableSlot[] = []
): TimeSlot[] {
  if (!Number.isInteger(duration) || duration <= 0) {
    throw new Error("Duration must be a positive integer.");
  }

  let start = new Date(startTime);
  let end = new Date(endTime);

  if (start >= end) {
    throw new Error("Start time must be before end time.");
  }

  let slots: TimeSlot[] = [];
  let currentTime = start;
  let endOfCurrentYear = new Date(
    currentTime.getFullYear(),
    11,
    31,
    23,
    59,
    59
  );

  while (currentTime < end) {
    let tempEndTime = endOfCurrentYear;
    if (tempEndTime > end) {
      tempEndTime = end;
    }
    while (currentTime < tempEndTime) {
      let isUnavailable = unavailableSlots.some((slot) => {
        let slotStart = new Date(slot.start);
        let slotEnd = new Date(slot.end);
        return currentTime >= slotStart && currentTime < slotEnd;
      });
      if (!isUnavailable) {
        let endSlot = new Date(currentTime.getTime());
        endSlot.setMinutes(endSlot.getMinutes() + duration);
        if (endSlot > end) endSlot = end;
        slots.push({
          start: currentTime.toISOString(),
          end: endSlot.toISOString(),
        });
      }
      currentTime = new Date(currentTime.getTime());
      currentTime.setMinutes(currentTime.getMinutes() + duration);
    }
    currentTime = new Date(currentTime.getFullYear() + 1, 0, 1);
    endOfCurrentYear = new Date(currentTime.getFullYear(), 11, 31, 23, 59, 59);
  }
  return slots;
}
```

## Usage

Here is how you can use the function in your code:

```javascript

const startTime = '2022-01-01T09:00:00.000Z';
const endTime = '2022-01-01T17:00:00.000Z';
const duration = 30;
const unavailableSlots = [
  { start: '2022-01-01T10:00:00.000Z', end: '2022-01-01T11:00:00.000Z' },
  { start: '2022-01-01T13:00:00.000Z', end: '2022-01-01T14:00:00.000Z' },
];

const timeSlots = generateTimeSlots(startTime, endTime, duration, unavailableSlots);

console.log(timeSlots);

```
