<template>
  <div class="mx-auto text-center p-4">
    <div class="sticky-container">
      <!-- Title -->
      <h1 class="text-3xl font-semibold mb-4">Weekly Schedule</h1>
      <!-- Disclaimer -->
      <p class="text-sm text-gray-500 mb-2">
        *All included for in-house guests.
      </p>
      <!-- Additional Information -->
      <p class="text-lg mb-16">Advanced booking is required.</p>
      <!-- Schedule Grid -->
      <div class="overflow-x-auto">
        <div class="schedule-grid">
          <!-- Time Slots -->
          <div
            class="p-6 border border-gray-300 border-x-0 border-t-0 time-slot"
          ></div>
          <!-- Days -->
          <div
            v-for="day in days"
            :key="day"
            class="p-6 border border-gray-300 border-r-0 border-y-0 day"
          >
            {{ day }}
          </div>
          <!-- Activities for Each Day -->
          <div v-for="time in timeSlots" :key="time" class="grid-row">
            <div
              class="pt-8 border border-gray-300 time-slot border-x-0 border-t-0 align-middle opacity-100"
            >
              {{ formatTime(time) }}
            </div>
            <!-- Display Activities -->
            <div
              v-for="day in days"
              :key="day + time"
              class="relative p-2 h-20 border border-gray-300 border-r-0 border-b-0"
            >
              <h4
                v-for="activity in getActivities(day, time)"
                :key="activity.name"
                class="m-auto text-left text-sm p-1 custom-width flex items-center"
                :style="getEventStyle(activity, day, time)"
              >
                {{ activity.name }}
              </h4>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { defineComponent } from "vue";

export default defineComponent({
  name: "WeeklySchedule",
  setup() {
    const activities = [
      {
        name: "Sunrise Yoga",
        color: "#E5DCCA",
        times: [
          { day: "Monday", startTime: 7, endTime: 7.5 },
          { day: "Wednesday", startTime: 7, endTime: 7.5 },
          { day: "Friday", startTime: 7, endTime: 7.5 },
        ],
      },
      {
        name: "Morning Yoga",
        color: "#E0E5D2",
        times: [
          { day: "Monday", startTime: 9, endTime: 10 },
          { day: "Tuesday", startTime: 9, endTime: 10 },
          { day: "Wednesday", startTime: 9, endTime: 10 },
          { day: "Thursday", startTime: 9, endTime: 10 },
          { day: "Friday", startTime: 9, endTime: 10 },
        ],
      },
      {
        name: "Aqua Stretching",
        color: "#EDD2A0",
        times: [
          { day: "Monday", startTime: 10.5, endTime: 11.25 },
          { day: "Wednesday", startTime: 10.5, endTime: 11.25 },
          { day: "Friday", startTime: 10.5, endTime: 11.25 },
        ],
      },
      {
        name: "Balinese Offerings",
        color: "#E5D0A7CE",
        times: [
          { day: "Monday", startTime: 11, endTime: 14 },
          { day: "Tuesday", startTime: 11, endTime: 14 },
          { day: "Wednesday", startTime: 11, endTime: 14 },
          { day: "Friday", startTime: 11, endTime: 14 },
        ],
      },
      {
        name: "Sounds Healing",
        color: "#CED5CE",
        times: [
          { day: "Monday", startTime: 14, endTime: 15.2 },
          { day: "Tuesday", startTime: 14, endTime: 15.2 },
          { day: "Wednesday", startTime: 14, endTime: 15.2 },
          { day: "Thursday", startTime: 14, endTime: 15.2 },
          { day: "Friday", startTime: 14, endTime: 15.2 },
          { day: "Saturday", startTime: 14, endTime: 15.2 },
          { day: "Sunday", startTime: 14, endTime: 15.2 },
        ],
      },
      {
        name: "Sunset Yoga",
        color: "#E3E5CD",
        times: [
          { day: "Monday", startTime: 18, endTime: 19 },
          { day: "Wednesday", startTime: 18, endTime: 19 },
          { day: "Thursday", startTime: 18, endTime: 19 },
          { day: "Friday", startTime: 18, endTime: 19 },
          { day: "Saturday", startTime: 18, endTime: 19 },
        ],
      },
      {
        name: "Alu Trekking",
        color: "#C2C9AB",
        times: [
          { day: "Tuesday", startTime: 10.5, endTime: 14 },
          { day: "Thursday", startTime: 10.5, endTime: 14 },
          { day: "Saturday", startTime: 10.5, endTime: 14 },
        ],
      },
      // {
      //   name: "Test Activity",
      //   color: "#FFFFFF",
      //   times: [
      //     { day: "Sunday", startTime: 9.5, endTime: 15 },
      //     { day: "Monday", startTime: 8.5, endTime: 10 },
      //     { day: "Friday", startTime: 9.5, endTime: 10.5 },
      //     { day: "Monday", startTime: 11.25, endTime: 16 },
      //   ],
      // },
    ];

    const generateTimeSlots = (activities) => {
      const timeSlotSet = new Set();
      activities.forEach((activity) => {
        activity.times.forEach((time) => {
          timeSlotSet.add(Math.floor(time.startTime));
          // Check if the duration is greater than or equal to 1
          if (time.endTime - time.startTime >= 1) {
            // If the duration is at least 1 hour, add the endTime to the timeSlotSet
            timeSlotSet.add(Math.floor(time.endTime));
          }
        });
      });

      // Convert the Set to an array and sort it
      const timeSlotsArray = Array.from(timeSlotSet).sort((a, b) => a - b);

      // Check if the last endTime isn't a float number and is the largest number among time slots in a day
      const lastEndTime = timeSlotsArray[timeSlotsArray.length - 1];
      if (
        Number.isInteger(lastEndTime) === true &&
        lastEndTime === Math.max(...timeSlotsArray)
      ) {
        // Remove the last endTime from the array
        timeSlotsArray.pop();
      }

      return timeSlotsArray;
    };

    const data = {
      days: [
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
        "Sunday",
      ],
      timeSlots: generateTimeSlots(activities),
    };

    // Function to filter activities for a specific day and time
    const getActivities = (day, time) => {
      return activities.filter((activity) =>
        activity.times.some(
          (slot) => slot.day === day && Math.floor(slot.startTime) === time
        )
      );
    };

    // Function to calculate height and position of activities
    const calculateHeightAndPosition = (startTime, endTime, timeSlots) => {
      const duration = endTime - startTime;
      let height;
      if (duration >= 2) {
        // If activity duration is greater than or equal to 2 hours
        const startIndex = timeSlots.indexOf(Math.floor(startTime));
        const endIndex = timeSlots.indexOf(Math.floor(endTime));

        if (Number.isInteger(startTime) === true) {
          height = `${
            (endIndex - startIndex) * 4 + (endIndex - startIndex - 1)
          }rem`;
        } else if (
          Number.isInteger(startTime) !== true &&
          Number.isInteger(endTime) !== true
        ) {
          height = `${(endIndex - startIndex) * 4 + (endTime % 1) * 4 + 1}rem`;
        } 
        else if(startTime % 1 < 0.5) {
          height = `${duration * 4 + 2}rem`;
        } else if(startTime % 1 > 0.5) {
          height = `${
            (endIndex - (startIndex + 1) + (startTime % 1)) * 4 +
            (endIndex - startIndex - 2)
          }rem`
        } 
        else {
          height = `${
            (endIndex - (startIndex + 1) + (startTime % 1)) * 4 +
            (endIndex - startIndex - 1)
          }rem`;
        }
      } else if (Number.isInteger(startTime) !== true && duration >= 1) {
        height = `${duration * 4 + 1}rem`; // For activities less than 2 hours with non-integer start time
      } else if ((Number.isInteger(endTime) !== true)) {
        height = `${duration * 4 + 0.5}rem`; // For activities less than 2 hours with non-integer end time
      } else if (duration <= 0) {
        height = 0; // For activities with zero or negative duration
      }
      else {
        height = `${duration * 4}rem`; // For activities with integer duration less than 2 hours
      }

      let startPos;
      if (Number.isInteger(startTime) !== true) {
        startPos = (startTime - parseInt(startTime)) * 4; // Start position for non-integer start time
      } else {
        startPos = (startTime % 1) * 4; // Start position for integer start time
      }

      return { height, marginTop: `${startPos}rem` };
    };

    // Function to style events based on their timing and duration
    const getEventStyle = (activity, day, time) => {
      const activityTime = activity.times.find(
        (slot) => slot.day === day && Math.floor(slot.startTime) === time
      );
      if (activityTime) {
        const { startTime, endTime } = activityTime;
        const { height, marginTop } = calculateHeightAndPosition(
          startTime,
          endTime,
          data.timeSlots
        );

        const overlappingActivities = activities
          .filter((overlapping) =>
            overlapping.times.some(
              (event) =>
                event.day === day &&
                ((event.startTime < endTime && event.endTime > startTime) ||
                  event.startTime === startTime)
            )
          )
          .sort(
            (a, b) =>
              a.times.find((t) => t.day === day).startTime -
              b.times.find((t) => t.day === day).startTime
          );

        const totalActivities = overlappingActivities.length;
        const index = overlappingActivities.findIndex(
          (overlapping) => overlapping.name === activity.name
        );

        let width = "90%";
        let left = "5%";

        if (totalActivities > 1) {
          width = `${80 / totalActivities}%`;
          left = `${
            5 + (totalActivities - 1 - index) * (90 / totalActivities)
          }%`;
        }

        return {
          backgroundColor: activity.color,
          height,
          marginTop,
          width,
          left,
        };
      }
      return {};
    };

    // Function to format time display
    const formatTime = (time) => {
      const hour = Math.floor(time);
      const suffix = hour >= 12 ? "PM" : "AM"; // Determine AM or PM
      const formattedHour = hour % 12 || 12; // Convert 24-hour format to 12-hour format
      return `${formattedHour} ${suffix}`;
    };

    // Return data and functions to be used in component
    return {
      ...data,
      getActivities,
      getEventStyle,
      formatTime,
    };
  },
});
</script>

<style scoped>
/* Scoped styles for component */

.custom-width {
  z-index: 9;
}
.sticky-container {
  position: sticky;
  left: 0;
  max-width: 1440px;
  margin: 0 auto;
  top: 0;
}

.schedule-grid {
  display: grid;
  grid-template-columns: 80px repeat(7, 1fr);
  gap: 0;
  width: 1440px;
}

/* Additional styling for schedule grid */

.time-slot {
  letter-spacing: 0.7px;
  color: #ad8c48;
  background-color: #efebe2;
  position: sticky;
  left: 0;
  z-index: 10;
}

.day {
  font: normal normal medium 14px/1px Basis Grotesque Arabic Pro;
  letter-spacing: 0px;
  color: #ad8c48;
  background-color: #efebe2;
  border-bottom: 1px solid rgba(173, 140, 72, 0.5);
}

.schedule-grid > .grid-row:last-child .activity-cell,
.schedule-grid > .grid-row:last-child .time-slot {
  border-bottom: none;
}

.grid-row {
  display: contents;
}

.activity-cell {
  border: 1px solid rgba(173, 140, 72, 0.5);
}

h4 {
  padding: 4px;
  border-radius: 5px;
  font: normal normal medium 12px/35px Basis Grotesque Arabic Pro;
  position: absolute;
}

h1 {
  font: normal normal normal 40px/50px Americana;
  letter-spacing: 0px;
  color: #ad8c48;
  opacity: 1;
}

.border-gray-300 {
  border-color: rgba(173, 140, 72, 0.5);
}
</style>
