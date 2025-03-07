
Recently, I had an exciting client use case: creating a customizable, modular form builder gem integrated with Active Admin. Here's the journey, some important insights, and lessons learned!

## 🤖 First Attempt: Leveraging AI

Initially, I thought, why not start with AI?

**Question:** Have you ever relied completely on AI to generate code?

While the AI-generated code provided an interesting starting point, it was quite messy, non-modular, and lacked the necessary customization needed for a robust open-source gem.

**Lesson Learned:** AI-generated code is a great starting point, but manual refinement and understanding of the underlying logic are crucial.

## 🛠️ Second Attempt: DIY Approach

I decided to take matters into my own hands. Building it manually taught me far more about the structure and internals of Active Admin gems.

### 📌 Key Learnings

**Question:** How do you ensure database compatibility in migrations?

Here's a neat trick to handle different databases (like PostgreSQL and others) in migrations:

```ruby
class CreateActiveFormSubmissions < ActiveRecord::Migration[6.0]
  def change
    create_table :active_form_submissions do |t|
      t.references :form, null: false, foreign_key: { to_table: :active_form_forms }
      t.references :resource, polymorphic: true, index: true
      t.<%= ActiveRecord::Base.connection.adapter_name.downcase == 'postgresql' ? 'jsonb' : 'json' %> :data, default: {}, null: false

      t.timestamps
    end

    <% if ActiveRecord::Base.connection.adapter_name.downcase == 'postgresql' %>
      add_index :active_form_submissions, :data, using: :gin
    <% end %>
  end
end
```

**Explanation:** This conditional ensures compatibility by dynamically checking the database adapter and choosing between `jsonb` (Postgres) and `json` (others).

### 📌 Modular, Extensible, Customizable

My DIY approach emphasized:
- Modular architecture
- Extensibility
- Customization

**Question:** What makes a gem great for open-source contributions?

- Clean structure
- Modular design
- Clear documentation
- Easy setup

These were my guiding principles, ensuring that the gem wasn't just good for me but beneficial to the community.

## 🔥 Interactive Q&A

**Q:** Should you rely fully on AI for creating complex, open-source gems?

**A:** Not fully. AI can give initial insights, but manual tweaking ensures clarity, modularity, and maintainability.

**Q:** What should you consider when writing database migrations for open-source gems?

**A:** Always account for database adapter compatibility. Conditional logic can ensure smooth usage across different environments.

## 🌟 Conclusion

Building a form builder gem for Active Admin has been a rewarding journey. Mistakes were lessons, AI provided insights, and manual coding refined my skills significantly.


