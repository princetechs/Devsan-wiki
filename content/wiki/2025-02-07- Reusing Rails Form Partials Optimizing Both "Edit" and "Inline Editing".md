
# Reusing Rails Form Partials: 

In modern Rails applications, it’s common to have multiple views that share similar or even identical form fields. In my project, I had a full-page edit form as well as an inline edit version for a candidate’s personal details. At first, I maintained two separate files, which made updates cumbersome and error-prone.

In this blog post, I’ll show you how I refactored the code to reuse the same form partial for both contexts. We’ll cover:

- **The original full-page form partial**
- **The inline editing form**
- **How to combine them using Rails’ `capture` helper and Turbo Frames**
- **Benefits and interactive tips for further customization**

---

## 1. The Original Full-Page Form Partial

My original form for the `/edit` panel was defined in a partial named `_form_personal_details.html.erb`. It looks something like this:

```erb
<!-- _form_personal_details.html.erb -->
<div class="container background-personal">
  <h4>Background & Personal</h4>
  
  <%= external_fields if local_assigns[:external_fields].present? %>

  <div class="row">
    <div class="col-md-4">
      <div class="form-group mb-3">
        <%= form.label :age, class: "form-label" %>
        <%= form.number_field :age, class: "form-control", placeholder: "Age" %>
      </div>
    </div>

    <div class="col-md-4">
      <div class="form-group mb-3">
        <%= form.label :family_status, "Family", class: "form-label" %>
        <%= form.text_field :family_status, class: "form-control", placeholder: "e.g., Married, with 2 kids" %>
      </div>
    </div>

    <!-- Additional fields for high_school, college, favorite_band, etc. -->

    <div class="col-md-4">
      <div class="form-group mb-3">
        <%= form.label :work_industry, class: "form-label" %>
        <%= form.text_field :work_industry, class: "form-control", placeholder: "Work Industry" %>
      </div>
    </div>
  </div>
</div>
```

This partial is used in a dedicated “edit” page where you might not need any inline actions.

---

## 2. The Inline Editing Form

I also had an inline editing form (used, for example, when editing details on a candidate’s profile page) defined separately:

```erb
<!-- _candidate_personal_details_form.html.erb -->
<%= form_with(model: candidate,
              url: update_personal_details_candidate_path(candidate),
              method: :patch) do |form| %>
  <div class="container background-personal position-relative">
    <div class="d-flex">
      <h4>Background & Personal</h4>
      <div class="actions" style="margin-left: 10px">
        <%= form.submit "Save", class: "btn btn-primary" %>
        <%= link_to "Cancel", candidate_path(candidate), class: "btn btn-secondary" %>
      </div>
    </div>
    <div class="row">
      <div class="col-md-4">
        <label>Age</label>
        <%= form.text_field :age, class: "form-control" %>
      </div>
      <div class="col-md-4">
        <label>Family</label>
        <%= form.text_field :family_status, class: "form-control" %>
      </div>
      <!-- More fields... -->
    </div>
  </div>
<% end %>
```

Notice how this inline form repeats many of the same input fields. Keeping two separate files means any update (e.g., a new field or a styling change) must be done twice. That’s when I decided to unify the code.

---

## 3. Unifying the Forms

The solution was to **reuse the full-page form partial** (_form_personal_details.html.erb_) and make it flexible enough to handle inline edit scenarios. The trick was to pass an extra “actions” block from the inline form to the partial.

### Step-by-Step Refactor

1. **Extract the common fields:**  
    The file `_form_personal_details.html.erb` already contains the common fields for both forms. We can use it directly.
    
2. **Make the form partial accept extra content:**  
    Notice at the top of the partial, we check for an `external_fields` local:
    
    ```erb
    <%= external_fields if local_assigns[:external_fields].present? %>
    ```
    
    This placeholder allows us to insert additional elements (like inline “Save” and “Cancel” buttons) when needed.
    
3. **Wrap your inline form in a Turbo Frame:**  
    In your inline edit view, wrap the form in a Turbo Frame. Then, capture the extra fields (in this case, the action buttons) using Rails’ `capture` helper.
    
    Here’s how the new inline edit file might look:
    
    ```erb
    <%= turbo_frame_tag "candidate_personal_details" do %>
      <%= form_with(model: @candidate,
                    url: update_personal_details_candidate_path(@candidate),
                    method: :patch) do |form| %>
    
        <%# Capture the extra fields for inline editing %>
        <% @external_fields = capture do %>
          <div class="actions" style="margin-left: 10px">
            <%= form.submit "Save", class: "btn btn-primary" %>
            <%= link_to "Cancel", candidate_path(@candidate), class: "btn btn-secondary" %>
          </div>
        <% end %>
    
        <%= render "form_personal_details", form: form, candidate: @candidate, external_fields: @external_fields %>
      <% end %>
    <% end %>
    ```
    
4. **Enjoy DRY Code:**  
    Now both the full-page `/edit` form and the inline edit form use the same `_form_personal_details.html.erb` partial. The inline version just adds the necessary actions block through the `external_fields` local.
    

---

## 4. Interactive Tips and Testing

### Try It Yourself!

- **Create the Shared Partial:**  
    Move all the common form code to `_form_personal_details.html.erb` as shown above.
- **Implement Conditional Extra Fields:**  
    In your partial, test the conditional rendering of `external_fields` by temporarily passing some sample content.
- **Use Turbo Frames:**  
    Experiment with Turbo Frames to update only the inline edit portion without reloading the entire page.
- **Test Both Scenarios:**  
    Ensure that:
    - On the full-page `/edit` view, the form displays without the inline action buttons.
    - On the inline edit view, the action buttons (Save/Cancel) appear as intended.

### What’s Happening Under the Hood?

- **`capture do` Block:**  
    The `capture` helper lets you grab the rendered HTML of a block into a variable. This is especially useful when you want to pass a block of content (like extra buttons) into a partial.
    
- **Turbo Frames:**  
    By wrapping the inline edit form with `turbo_frame_tag`, any updates or form submissions can refresh just that part of the page, giving a smoother user experience.
    
- **Code Reusability:**  
    By having one source of truth for your form fields, any future changes (e.g., adding a new field or updating placeholder text) need to be made only once.
    

---

## 5. Conclusion

Refactoring your Rails forms to share a single partial is a win for maintainability and consistency. By leveraging Rails’ built-in helpers like `capture` and modern tools like Turbo Frames, you can create interactive, dynamic forms that serve multiple purposes with less code duplication.

Feel free to experiment further—maybe add some JavaScript interactivity or integrate with Stimulus for even richer inline experiences. Happy coding!

---

_Have questions or want to share your experience? Drop a comment below or reach out on social media!_